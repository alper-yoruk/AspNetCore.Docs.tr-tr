---
title: Apache ile Linux üzerinde ASP.NET Core barındırma
author: rick-anderson
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirmek için CentOS üzerinde bir ters proxy sunucusu olarak Apache 'yi ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0bae3f888a1b7a3c2860b85754779189c636d86f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057706"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="b539a-103">Apache ile Linux üzerinde ASP.NET Core barındırma</span><span class="sxs-lookup"><span data-stu-id="b539a-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="b539a-104">Sağlayan- [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="b539a-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="b539a-105">Bu kılavuzu kullanarak, HTTP trafiğinin [Kestrel](xref:fundamentals/servers/kestrel) Server üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirilmesini sağlamak Için [CentOS 7](https://www.centos.org/) ' de bir ters proxy sunucusu olarak [Apache](https://httpd.apache.org/) 'yi ayarlamayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="b539a-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b539a-106">[Mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili Modüller sunucunun ters proxy 'sini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b539a-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b539a-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="b539a-107">Prerequisites</span></span>

* <span data-ttu-id="b539a-108">Sudo ayrıcalığına sahip standart bir kullanıcı hesabıyla CentOS 7 çalıştıran sunucu.</span><span class="sxs-lookup"><span data-stu-id="b539a-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="b539a-109">.NET Core çalışma zamanını sunucuya yükler.</span><span class="sxs-lookup"><span data-stu-id="b539a-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="b539a-110">[.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="b539a-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="b539a-111">En son Önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="b539a-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="b539a-112">**Uygulama çalıştırma-çalışma zamanı** altındaki tabloda en son önizleme dışı çalışma zamanını indirin.</span><span class="sxs-lookup"><span data-stu-id="b539a-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="b539a-113">Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve CentOS talimatlarını izleyin.</span><span class="sxs-lookup"><span data-stu-id="b539a-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="b539a-114">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b539a-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="b539a-115">Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b539a-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="b539a-116">Uygulama üzerinde Yayımla ve Kopyala</span><span class="sxs-lookup"><span data-stu-id="b539a-116">Publish and copy over the app</span></span>

<span data-ttu-id="b539a-117">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b539a-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="b539a-118">Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="b539a-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="b539a-119">Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b539a-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="b539a-120">Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b539a-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="b539a-121">`https://localhost:5001` `applicationUrl` Dosyadaki *Properties/launchSettings.js* özelliğinden (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b539a-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="b539a-122">Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/ &lt; target_framework_moniker &gt; /Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b539a-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="b539a-123">Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="b539a-124">ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b539a-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="b539a-125">*Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="b539a-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="b539a-126">Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="b539a-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="b539a-127">Proxy sunucusu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b539a-127">Configure a proxy server</span></span>

<span data-ttu-id="b539a-128">Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir.</span><span class="sxs-lookup"><span data-stu-id="b539a-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="b539a-129">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="b539a-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="b539a-130">Proxy sunucusu, istemci isteklerini istekleri yerine başka bir sunucuya ileten bir sunucu olur.</span><span class="sxs-lookup"><span data-stu-id="b539a-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="b539a-131">Ters proxy, genellikle rastgele istemciler adına sabit bir hedefe iletilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="b539a-132">Bu kılavuzda Apache, Kestrel 'in ASP.NET Core uygulamasına hizmet veren aynı sunucuda çalışan ters proxy olarak yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b539a-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="b539a-133">İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="b539a-134">Ara yazılım, `Request.Scheme` üstbilgiyi kullanarak, `X-Forwarded-Proto` yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="b539a-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="b539a-135">Bir şemaya bağlı kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirme ve coğrafi konum gibi herhangi bir bileşen, Iletilen üstbilgiler ara yazılımı çağrıldıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b539a-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="b539a-136"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce yönteminin en üstünde yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="b539a-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="b539a-137">Ara yazılımı, `X-Forwarded-For` ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` :</span><span class="sxs-lookup"><span data-stu-id="b539a-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="b539a-138">Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None` .</span><span class="sxs-lookup"><span data-stu-id="b539a-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="b539a-139">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir.</span><span class="sxs-lookup"><span data-stu-id="b539a-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="b539a-140">Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya ile listesine ekleyin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> .</span><span class="sxs-lookup"><span data-stu-id="b539a-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="b539a-141">Aşağıdaki örnek, içindeki Iletilen üstbilgiler ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler `KnownProxies` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b539a-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="b539a-142">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b539a-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="b539a-143">Apache 'yi yükler</span><span class="sxs-lookup"><span data-stu-id="b539a-143">Install Apache</span></span>

<span data-ttu-id="b539a-144">CentOS paketlerini en son kararlı sürümlerine güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b539a-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="b539a-145">Tek bir komutla, CentOS üzerinde Apache Web sunucusunu yükler `yum` :</span><span class="sxs-lookup"><span data-stu-id="b539a-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="b539a-146">Komutu çalıştırdıktan sonra örnek çıkış:</span><span class="sxs-lookup"><span data-stu-id="b539a-146">Sample output after running the command:</span></span>

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
> <span data-ttu-id="b539a-147">Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıkış httpd.86_64 yansıtır.</span><span class="sxs-lookup"><span data-stu-id="b539a-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="b539a-148">Apache 'nin yüklü olduğu yeri doğrulamak için `whereis httpd` komut isteminden komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b539a-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="b539a-149">Apache yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b539a-149">Configure Apache</span></span>

<span data-ttu-id="b539a-150">Apache için yapılandırma dosyaları, dizin içinde bulunur `/etc/httpd/conf.d/` .</span><span class="sxs-lookup"><span data-stu-id="b539a-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="b539a-151">*. Conf* uzantısına sahip herhangi bir dosya, içindeki modül yapılandırma dosyalarının yanı sıra `/etc/httpd/conf.modules.d/` , modülleri yüklemek için gereken yapılandırma dosyalarını içeren alfabetik sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="b539a-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="b539a-152">Uygulama için *HelloApp. conf* adlı bir yapılandırma dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b539a-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

<span data-ttu-id="b539a-153">`VirtualHost`Blok, sunucuda bir veya daha fazla dosyada birden çok kez görünebilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="b539a-154">Yukarıdaki yapılandırma dosyasında Apache, 80 numaralı bağlantı noktasında genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b539a-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="b539a-155">Etki alanına `www.example.com` sunulmakta ve `*.example.com` diğer ad aynı Web sitesine çözümlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="b539a-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="b539a-156">Daha fazla bilgi için bkz. [ad tabanlı sanal konak desteği](https://httpd.apache.org/docs/current/vhosts/name-based.html) .</span><span class="sxs-lookup"><span data-stu-id="b539a-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="b539a-157">İstekler, kökte, sunucunun bağlantı noktası 5000 ' den 127.0.0.1 ' de sunucu üzerinden alınır.</span><span class="sxs-lookup"><span data-stu-id="b539a-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="b539a-158">İki yönlü iletişim için `ProxyPass` `ProxyPassReverse` gereklidir ve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="b539a-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="b539a-159">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="b539a-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="b539a-160">**VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="b539a-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="b539a-161">Alt etki alanı joker karakteri bağlama (örneğin, `*.example.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz `*.com` .</span><span class="sxs-lookup"><span data-stu-id="b539a-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b539a-162">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="b539a-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="b539a-163">Günlüğe kaydetme, `VirtualHost` `ErrorLog` ve yönergeleri başına yapılandırılabilir `CustomLog` .</span><span class="sxs-lookup"><span data-stu-id="b539a-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="b539a-164">`ErrorLog` , sunucunun hataları günlüğe kaydettiği konumdur ve `CustomLog` günlük dosyasının dosya adını ve biçimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b539a-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="b539a-165">Bu durumda istek bilgileri günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="b539a-166">Her istek için bir satır vardır.</span><span class="sxs-lookup"><span data-stu-id="b539a-166">There's one line for each request.</span></span>

<span data-ttu-id="b539a-167">Dosyayı kaydedin ve yapılandırmayı test edin.</span><span class="sxs-lookup"><span data-stu-id="b539a-167">Save the file and test the configuration.</span></span> <span data-ttu-id="b539a-168">Her şey geçerse yanıtın olması gerekir `Syntax [OK]` .</span><span class="sxs-lookup"><span data-stu-id="b539a-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="b539a-169">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="b539a-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="b539a-170">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="b539a-170">Monitor the app</span></span>

<span data-ttu-id="b539a-171">Apache, `http://localhost:80` ' de Kestrel üzerinde çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek için kurulum kuruyor `http://127.0.0.1:5000` .</span><span class="sxs-lookup"><span data-stu-id="b539a-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="b539a-172">Ancak, Kestrel işlemini yönetmek için Apache ayarlanmamış.</span><span class="sxs-lookup"><span data-stu-id="b539a-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="b539a-173">Temel Web uygulamasını başlatmak ve izlemek için *systemd* ve hizmet dosyası oluşturma ' yı kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="b539a-174">*systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="b539a-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="b539a-175">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="b539a-175">Create the service file</span></span>

<span data-ttu-id="b539a-176">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b539a-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="b539a-177">Uygulama için örnek bir hizmet dosyası:</span><span class="sxs-lookup"><span data-stu-id="b539a-177">An example service file for the app:</span></span>

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

<span data-ttu-id="b539a-178">Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-178">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="b539a-179">Kullanıcı ( `apache` ) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.</span><span class="sxs-lookup"><span data-stu-id="b539a-179">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="b539a-180">`TimeoutStopSec`Uygulamanın ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-180">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="b539a-181">Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="b539a-181">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="b539a-182">Değeri unitless saniyeler (örneğin, `150` ), bir zaman aralığı değeri (örneğin, `2min 30s` ) veya `infinity` zaman aşımını devre dışı bırakmak için girin.</span><span class="sxs-lookup"><span data-stu-id="b539a-182">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="b539a-183">`TimeoutStopSec` Varsayılan olarak, `DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değerini alır (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="b539a-183">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="b539a-184">Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="b539a-184">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="b539a-185">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b539a-185">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="b539a-186">Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b539a-186">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b539a-187">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b539a-187">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="b539a-188">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-188">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="b539a-189">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="b539a-189">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="b539a-190">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="b539a-190">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b539a-191">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b539a-191">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="b539a-192">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-192">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="b539a-193">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="b539a-193">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="b539a-194">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="b539a-194">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="b539a-195">Dosyayı kaydedin ve hizmeti etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="b539a-195">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="b539a-196">Hizmeti başlatın ve çalıştığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="b539a-196">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="b539a-197">Ters proxy yapılandırılmış ve *systemd* üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden yerel makinedeki bir tarayıcıdan erişilebilir `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="b539a-197">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="b539a-198">Yanıt üst bilgilerini inceleyerek **sunucu** üst bilgisi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="b539a-198">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="b539a-199">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="b539a-199">View logs</span></span>

<span data-ttu-id="b539a-200">Kestrel kullanan Web uygulaması *systemd* kullanılarak yönetildiğinden, olaylar ve süreçler merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-200">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="b539a-201">Ancak, bu günlük *systemd* tarafından yönetilen tüm hizmet ve işlemlere ait girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="b539a-201">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="b539a-202">`kestrel-helloapp.service`Belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b539a-202">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="b539a-203">Zaman filtreleme için komutuyla saat seçeneklerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="b539a-203">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="b539a-204">Örneğin, `--since today` geçerli güne filtre uygulamak veya `--until 1 hour ago` önceki saatin girişlerini görmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-204">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="b539a-205">Daha fazla bilgi için bkz. [journalctl için man sayfası](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="b539a-205">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="b539a-206">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="b539a-206">Data protection</span></span>

<span data-ttu-id="b539a-207">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı ( [](xref:fundamentals/middleware/index)Örneğin, cookie Ara yazılım) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core middlewares tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b539a-207">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="b539a-208">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b539a-208">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="b539a-209">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="b539a-209">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="b539a-210">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="b539a-210">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="b539a-211">Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="b539a-211">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="b539a-212">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b539a-212">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="b539a-213">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="b539a-213">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="b539a-214">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-214">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="b539a-215">Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:</span><span class="sxs-lookup"><span data-stu-id="b539a-215">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="b539a-216">Uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="b539a-216">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="b539a-217">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b539a-217">Configure firewall</span></span>

<span data-ttu-id="b539a-218">*Firewallld* , ağ bölgeleri desteğiyle güvenlik duvarını yönetmek için dinamik bir Daemon.</span><span class="sxs-lookup"><span data-stu-id="b539a-218">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="b539a-219">Bağlantı noktaları ve paket filtrelemesi, Iptables tarafından hala yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-219">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="b539a-220">*Firewalld* , varsayılan olarak yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="b539a-220">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="b539a-221">`yum` paketi yüklemek veya yüklendiğini doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b539a-221">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="b539a-222">`firewalld`Yalnızca uygulama için gerekli olan bağlantı noktalarını açmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-222">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="b539a-223">Bu durumda, 80 ve 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b539a-223">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="b539a-224">Aşağıdaki komutlar şunları açmak için 80 ve 443 bağlantı noktalarını kalıcı olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b539a-224">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="b539a-225">Güvenlik Duvarı ayarlarını yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="b539a-225">Reload the firewall settings.</span></span> <span data-ttu-id="b539a-226">Varsayılan bölgedeki kullanılabilir hizmetleri ve bağlantı noktalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b539a-226">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="b539a-227">Seçenekler inceleyerek kullanılabilir `firewall-cmd -h` .</span><span class="sxs-lookup"><span data-stu-id="b539a-227">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="b539a-228">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b539a-228">HTTPS configuration</span></span>

<span data-ttu-id="b539a-229">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="b539a-229">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="b539a-230">[DotNet Run](/dotnet/core/tools/dotnet-run) komutu, uygulamayı özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandıran, uygulamanın *Özellikler/launchSettings.js* kullanır `applicationUrl` (örneğin, `https://localhost:5001;http://localhost:5000` ).</span><span class="sxs-lookup"><span data-stu-id="b539a-230">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="b539a-231">`dotnet run`Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + f5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="b539a-231">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="b539a-232">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="b539a-232">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="b539a-233">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="b539a-233">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="b539a-234">**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="b539a-234">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="b539a-235">HTTPS için Apache 'yi yapılandırmak için *mod_ssl* modülü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b539a-235">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="b539a-236">*Httpd* modülü yüklendiğinde *mod_ssl* modülü de yüklendi.</span><span class="sxs-lookup"><span data-stu-id="b539a-236">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="b539a-237">Yüklenmemişse, `yum` yapılandırmaya eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-237">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="b539a-238">HTTPS 'yi zorlamak için, `mod_rewrite` URL yeniden yazmayı etkinleştirmek üzere modülünü yükler:</span><span class="sxs-lookup"><span data-stu-id="b539a-238">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="b539a-239">Bağlantı noktası 443 üzerinde URL yeniden yazma ve güvenli iletişim sağlamak için *HelloApp. conf* dosyasını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b539a-239">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="b539a-240">Bu örnek, yerel olarak üretilmiş bir sertifika kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="b539a-240">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="b539a-241">**Sslcertificatefile** , etki alanı adı için birincil sertifika dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b539a-241">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="b539a-242">**Sslcertificatekeyfile** , CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b539a-242">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="b539a-243">**Sslcertificatechainfile** , sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b539a-243">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="b539a-244">Dosyayı kaydedin ve yapılandırmayı test edin:</span><span class="sxs-lookup"><span data-stu-id="b539a-244">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="b539a-245">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="b539a-245">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="b539a-246">Ek Apache önerileri</span><span class="sxs-lookup"><span data-stu-id="b539a-246">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="b539a-247">Paylaşılan çerçeve güncelleştirmeleriyle uygulamaları yeniden başlatma</span><span class="sxs-lookup"><span data-stu-id="b539a-247">Restart apps with shared framework updates</span></span>

<span data-ttu-id="b539a-248">Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b539a-248">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="b539a-249">Ek üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="b539a-249">Additional headers</span></span>

<span data-ttu-id="b539a-250">Kötü amaçlı saldırılara karşı korumak için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="b539a-250">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="b539a-251">Modülün yüklü olduğundan emin olun `mod_headers` :</span><span class="sxs-lookup"><span data-stu-id="b539a-251">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="b539a-252">Tıklama ve tıklama saldırılarına karşı güvenli Apache</span><span class="sxs-lookup"><span data-stu-id="b539a-252">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="b539a-253">*UI redki saldırısı* olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur.</span><span class="sxs-lookup"><span data-stu-id="b539a-253">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="b539a-254">`X-FRAME-OPTIONS`Sitesini güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b539a-254">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="b539a-255">Tıklama saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="b539a-255">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="b539a-256">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="b539a-256">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="b539a-257">`Header append X-FRAME-OPTIONS "SAMEORIGIN"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b539a-257">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="b539a-258">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b539a-258">Save the file.</span></span>
1. <span data-ttu-id="b539a-259">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b539a-259">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="b539a-260">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="b539a-260">MIME-type sniffing</span></span>

<span data-ttu-id="b539a-261">`X-Content-Type-Options`Üst bilgi, Internet Explorer 'ın *MIME algılaması* (dosyanın içeriğinden bir dosya belirleme `Content-Type` ) gerçekleştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="b539a-261">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="b539a-262">Sunucu `Content-Type` üstbilgiyi `text/html` seçenek kümesiyle ayarlarsa `nosniff` , Internet Explorer içeriği `text/html` dosyanın içeriğinden bağımsız olarak işler.</span><span class="sxs-lookup"><span data-stu-id="b539a-262">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="b539a-263">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="b539a-263">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="b539a-264">`Header set X-Content-Type-Options "nosniff"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b539a-264">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="b539a-265">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b539a-265">Save the file.</span></span> <span data-ttu-id="b539a-266">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b539a-266">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="b539a-267">YükDengeleme</span><span class="sxs-lookup"><span data-stu-id="b539a-267">Load Balancing</span></span>

<span data-ttu-id="b539a-268">Bu örnek, CentOS 7 ve Kestrel üzerinde Apache 'in aynı örnek makinede nasıl ayarlanacağını ve yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="b539a-268">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="b539a-269">Tek bir hata noktası olmaması için; *mod_proxy_balancer* kullanmak ve **VirtualHost** 'u değiştirmek, Apache proxy sunucusunun arkasındaki Web uygulamalarının birden çok örneğini yönetmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b539a-269">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="b539a-270">Aşağıda gösterilen yapılandırma dosyasında, bağlantı noktası 5001 ' de çalışacak ek bir örneği `helloapp` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b539a-270">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="b539a-271">*Proxy* bölümü, Yük Dengeleme *istekleri* için iki üyeli bir dengeleyici yapılandırması ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b539a-271">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="b539a-272">Oran limitleri</span><span class="sxs-lookup"><span data-stu-id="b539a-272">Rate Limits</span></span>

<span data-ttu-id="b539a-273">*Httpd* modülüne eklenen *mod_ratelimit* kullanarak, istemcilerin bant genişliği sınırlandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="b539a-273">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="b539a-274">Örnek dosya, bant genişliğini kök konumu altında 600 KB/sn olarak sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="b539a-274">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="b539a-275">Uzun istek üst bilgisi alanları</span><span class="sxs-lookup"><span data-stu-id="b539a-275">Long request header fields</span></span>

<span data-ttu-id="b539a-276">Proxy sunucusu varsayılan ayarları, istek üst bilgisi alanlarını genellikle 8.190 bayt ile sınırlar.</span><span class="sxs-lookup"><span data-stu-id="b539a-276">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="b539a-277">Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="b539a-277">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="b539a-278">Daha uzun alanlar gerekliyse, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlamayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b539a-278">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="b539a-279">Uygulanacak değer senaryoya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b539a-279">The value to apply depends on the scenario.</span></span> <span data-ttu-id="b539a-280">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="b539a-280">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="b539a-281">Gerekli olmadığı takdirde varsayılan değerini artırmaz `LimitRequestFieldSize` .</span><span class="sxs-lookup"><span data-stu-id="b539a-281">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="b539a-282">Değerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="b539a-282">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b539a-283">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b539a-283">Additional resources</span></span>

* [<span data-ttu-id="b539a-284">Linux üzerinde .NET Core önkoşulları</span><span class="sxs-lookup"><span data-stu-id="b539a-284">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
