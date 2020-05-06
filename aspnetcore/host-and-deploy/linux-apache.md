---
title: Apache ile Linux üzerinde ASP.NET Core barındırma
author: rick-anderson
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirmek için CentOS üzerinde bir ters proxy sunucusu olarak Apache 'yi ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 9f0825f65f316ee4caf67e82fe5812e3a1ae813e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775914"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="c4e5e-103">Apache ile Linux üzerinde ASP.NET Core barındırma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="c4e5e-104">Sağlayan- [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="c4e5e-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="c4e5e-105">Bu kılavuzu kullanarak, HTTP trafiğinin [Kestrel](xref:fundamentals/servers/kestrel) Server üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirilmesini sağlamak Için [CentOS 7](https://www.centos.org/) ' de bir ters proxy sunucusu olarak [Apache](https://httpd.apache.org/) 'yi ayarlamayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="c4e5e-106">[Mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili Modüller sunucunun ters proxy 'sini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c4e5e-107">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="c4e5e-107">Prerequisites</span></span>

* <span data-ttu-id="c4e5e-108">Sudo ayrıcalığına sahip standart bir kullanıcı hesabıyla CentOS 7 çalıştıran sunucu.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="c4e5e-109">.NET Core çalışma zamanını sunucuya yükler.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="c4e5e-110">[.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="c4e5e-111">En son Önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="c4e5e-112">**Uygulama çalıştırma-çalışma zamanı**altındaki tabloda en son önizleme dışı çalışma zamanını indirin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="c4e5e-113">Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve CentOS talimatlarını izleyin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="c4e5e-114">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="c4e5e-115">Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="c4e5e-116">Uygulama üzerinde Yayımla ve Kopyala</span><span class="sxs-lookup"><span data-stu-id="c4e5e-116">Publish and copy over the app</span></span>

<span data-ttu-id="c4e5e-117">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="c4e5e-118">Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="c4e5e-119">Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="c4e5e-120">Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="c4e5e-121">`https://localhost:5001` *Properties/launchsettings. JSON* dosyasındaki `applicationUrl` özelliğinden (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="c4e5e-122">Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/&lt;target_framework_moniker&gt;/Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="c4e5e-123">Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="c4e5e-124">ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="c4e5e-125">*Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="c4e5e-126">Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="c4e5e-127">Proxy sunucusu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-127">Configure a proxy server</span></span>

<span data-ttu-id="c4e5e-128">Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="c4e5e-129">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="c4e5e-130">Proxy sunucusu, istemci isteklerini istekleri yerine başka bir sunucuya ileten bir sunucu olur.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="c4e5e-131">Ters proxy, genellikle rastgele istemciler adına sabit bir hedefe iletilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="c4e5e-132">Bu kılavuzda Apache, Kestrel 'in ASP.NET Core uygulamasına hizmet veren aynı sunucuda çalışan ters proxy olarak yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="c4e5e-133">İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="c4e5e-134">Ara yazılım, `X-Forwarded-Proto` üstbilgiyi `Request.Scheme`kullanarak, yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="c4e5e-135">Bir şemaya bağlı kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirme ve coğrafi konum gibi herhangi bir bileşen, Iletilen üstbilgiler ara yazılımı çağrıldıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="c4e5e-136">Genel bir kural olarak, Iletilen üstbilgiler ara yazılımı, tanılama ve hata işleme ara yazılımı dışında diğer ara yazılım ile önce çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="c4e5e-137">Bu sıralama, iletilen üst bilgi bilgilerine bağlı olan ara yazılımın işleme için üst bilgi değerlerini kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="c4e5e-138">Diğer ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> yazılım çağrılmadan `Startup.Configure` önce yönteminin en üstünde yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="c4e5e-139">Ara yazılımı, `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini iletecek şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="c4e5e-140">Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None`.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="c4e5e-141">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="c4e5e-142">Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> ile <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="c4e5e-143">Aşağıdaki örnek, içindeki `KnownProxies` `Startup.ConfigureServices`iletilen ÜSTBILGILER ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="c4e5e-144">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="c4e5e-145">Apache 'yi yükler</span><span class="sxs-lookup"><span data-stu-id="c4e5e-145">Install Apache</span></span>

<span data-ttu-id="c4e5e-146">CentOS paketlerini en son kararlı sürümlerine güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="c4e5e-147">Tek `yum` bir komutla, CentOS üzerinde Apache Web sunucusunu yükler:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="c4e5e-148">Komutu çalıştırdıktan sonra örnek çıkış:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-148">Sample output after running the command:</span></span>

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
> <span data-ttu-id="c4e5e-149">Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıkış httpd. 86_64 ' i yansıtır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="c4e5e-150">Apache 'nin yüklü olduğu yeri doğrulamak için komut `whereis httpd` isteminden komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="c4e5e-151">Apache yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-151">Configure Apache</span></span>

<span data-ttu-id="c4e5e-152">Apache için yapılandırma dosyaları, `/etc/httpd/conf.d/` dizin içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="c4e5e-153">*. Conf* uzantısına sahip herhangi bir dosya, içindeki `/etc/httpd/conf.modules.d/`modül yapılandırma dosyalarının yanı sıra, modülleri yüklemek için gereken yapılandırma dosyalarını içeren alfabetik sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="c4e5e-154">Uygulama için *HelloApp. conf*adlı bir yapılandırma dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

<span data-ttu-id="c4e5e-155">`VirtualHost` Blok, sunucuda bir veya daha fazla dosyada birden çok kez görünebilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="c4e5e-156">Yukarıdaki yapılandırma dosyasında Apache, 80 numaralı bağlantı noktasında genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="c4e5e-157">Etki alanına `www.example.com` sunulmakta ve `*.example.com` diğer ad aynı Web sitesine çözümlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="c4e5e-158">Daha fazla bilgi için bkz. [ad tabanlı sanal konak desteği](https://httpd.apache.org/docs/current/vhosts/name-based.html) .</span><span class="sxs-lookup"><span data-stu-id="c4e5e-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="c4e5e-159">İstekler, kökte, sunucunun bağlantı noktası 5000 ' den 127.0.0.1 ' de sunucu üzerinden alınır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="c4e5e-160">İki yönlü iletişim için gereklidir `ProxyPass` ve `ProxyPassReverse` gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="c4e5e-161">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="c4e5e-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="c4e5e-162">**VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="c4e5e-163">Alt etki alanı joker karakteri bağlama ( `*.example.com`Örneğin,), tüm üst etki alanını (Bu güvenlik açığı olan aksine `*.com`) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c4e5e-164">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="c4e5e-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="c4e5e-165">Günlüğe kaydetme, `ErrorLog` ve `CustomLog` yönergeleri `VirtualHost` başına yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="c4e5e-166">`ErrorLog`, sunucunun hataları günlüğe kaydettiği konumdur ve `CustomLog` günlük dosyasının dosya adını ve biçimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="c4e5e-167">Bu durumda istek bilgileri günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="c4e5e-168">Her istek için bir satır vardır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-168">There's one line for each request.</span></span>

<span data-ttu-id="c4e5e-169">Dosyayı kaydedin ve yapılandırmayı test edin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-169">Save the file and test the configuration.</span></span> <span data-ttu-id="c4e5e-170">Her şey geçerse yanıtın olması `Syntax [OK]`gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="c4e5e-171">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="c4e5e-172">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="c4e5e-172">Monitor the app</span></span>

<span data-ttu-id="c4e5e-173">Apache, ' de Kestrel üzerinde çalışan ASP.NET Core uygulamasına `http://localhost:80` yapılan istekleri iletmek için kurulum kuruyor `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="c4e5e-174">Ancak, Kestrel işlemini yönetmek için Apache ayarlanmamış.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="c4e5e-175">Temel Web uygulamasını başlatmak ve izlemek için *systemd* ve hizmet dosyası oluşturma ' yı kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="c4e5e-176">*systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="c4e5e-177">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-177">Create the service file</span></span>

<span data-ttu-id="c4e5e-178">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="c4e5e-179">Uygulama için örnek bir hizmet dosyası:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-179">An example service file for the app:</span></span>

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

<span data-ttu-id="c4e5e-180">Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-180">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="c4e5e-181">Kullanıcı (`apache`) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-181">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="c4e5e-182">Uygulamanın `TimeoutStopSec` ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-182">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="c4e5e-183">Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-183">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="c4e5e-184">Değeri unitless saniyeler (örneğin, `150`), bir zaman aralığı değeri (örneğin, `2min 30s`) veya `infinity` zaman aşımını devre dışı bırakmak için girin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-184">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="c4e5e-185">`TimeoutStopSec`Varsayılan olarak, yönetici yapılandırma `DefaultTimeoutStopSec` dosyasındaki değerini alır (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="c4e5e-185">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="c4e5e-186">Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-186">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="c4e5e-187">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-187">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="c4e5e-188">Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-188">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="c4e5e-189">İki nokta`:`() ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-189">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="c4e5e-190">İki nokta üst üste yerine`__`çift alt çizgi () kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-190">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="c4e5e-191">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-191">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="c4e5e-192">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde `ConnectionStrings__DefaultConnection`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-192">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="c4e5e-193">Dosyayı kaydedin ve hizmeti etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-193">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="c4e5e-194">Hizmeti başlatın ve çalıştığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-194">Start the service and verify that it's running:</span></span>

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

<span data-ttu-id="c4e5e-195">Ters proxy yapılandırılmış ve *systemd*üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden `http://localhost`yerel makinedeki bir tarayıcıdan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-195">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="c4e5e-196">Yanıt üst bilgilerini inceleyerek **sunucu** üst bilgisi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-196">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="c4e5e-197">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="c4e5e-197">View logs</span></span>

<span data-ttu-id="c4e5e-198">Kestrel kullanan Web uygulaması *systemd*kullanılarak yönetildiğinden, olaylar ve süreçler merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-198">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="c4e5e-199">Ancak, bu günlük *systemd*tarafından yönetilen tüm hizmet ve işlemlere ait girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-199">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="c4e5e-200">Belirli öğeleri görüntülemek `kestrel-helloapp.service`için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-200">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="c4e5e-201">Zaman filtreleme için komutuyla saat seçeneklerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-201">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="c4e5e-202">Örneğin, geçerli güne `--since today` filtre uygulamak veya `--until 1 hour ago` önceki saatin girişlerini görmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-202">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="c4e5e-203">Daha fazla bilgi için bkz. [journalctl için man sayfası](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="c4e5e-203">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="c4e5e-204">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-204">Data protection</span></span>

<span data-ttu-id="c4e5e-205">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı (örneğin, tanımlama bilgisi ara yazılımı) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-205">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="c4e5e-206">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-206">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c4e5e-207">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-207">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c4e5e-208">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-208">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c4e5e-209">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-209">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="c4e5e-210">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-210">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="c4e5e-211">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-211">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c4e5e-212">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata)içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-212">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c4e5e-213">Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-213">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="c4e5e-214">Uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="c4e5e-214">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="c4e5e-215">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-215">Configure firewall</span></span>

<span data-ttu-id="c4e5e-216">*Firewallld* , ağ bölgeleri desteğiyle güvenlik duvarını yönetmek için dinamik bir Daemon.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-216">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="c4e5e-217">Bağlantı noktaları ve paket filtrelemesi, Iptables tarafından hala yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-217">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="c4e5e-218">*Firewalld* , varsayılan olarak yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-218">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="c4e5e-219">`yum`paketi yüklemek veya yüklendiğini doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-219">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="c4e5e-220">Yalnızca `firewalld` uygulama için gerekli olan bağlantı noktalarını açmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-220">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="c4e5e-221">Bu durumda, 80 ve 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-221">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="c4e5e-222">Aşağıdaki komutlar şunları açmak için 80 ve 443 bağlantı noktalarını kalıcı olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-222">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="c4e5e-223">Güvenlik Duvarı ayarlarını yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-223">Reload the firewall settings.</span></span> <span data-ttu-id="c4e5e-224">Varsayılan bölgedeki kullanılabilir hizmetleri ve bağlantı noktalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-224">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="c4e5e-225">Seçenekler inceleyerek `firewall-cmd -h`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-225">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="c4e5e-226">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c4e5e-226">HTTPS configuration</span></span>

<span data-ttu-id="c4e5e-227">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="c4e5e-227">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="c4e5e-228">[DotNet Run](/dotnet/core/tools/dotnet-run) komutu uygulamanın *Özellikler/launchsettings. JSON* dosyasını kullanır, bu da uygulamayı `applicationUrl` özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandırır (örneğin, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="c4e5e-228">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="c4e5e-229">Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı `dotnet run` komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + F5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-229">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="c4e5e-230">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="c4e5e-230">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="c4e5e-231">KestrelServerOptions. ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="c4e5e-231">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="c4e5e-232">**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="c4e5e-232">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="c4e5e-233">HTTPS için Apache 'yi yapılandırmak için *mod_ssl* modülü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-233">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="c4e5e-234">*Httpd* modülü yüklendiğinde *mod_ssl* modülü de yüklendi.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-234">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="c4e5e-235">Yüklenmemişse, yapılandırmaya eklemek için kullanın `yum` .</span><span class="sxs-lookup"><span data-stu-id="c4e5e-235">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="c4e5e-236">HTTPS 'yi zorlamak için, URL `mod_rewrite` yeniden yazmayı etkinleştirmek üzere modülünü yükler:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-236">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="c4e5e-237">Bağlantı noktası 443 üzerinde URL yeniden yazma ve güvenli iletişim sağlamak için *HelloApp. conf* dosyasını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-237">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="c4e5e-238">Bu örnek, yerel olarak üretilmiş bir sertifika kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-238">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="c4e5e-239">**Sslcertificatefile** , etki alanı adı için birincil sertifika dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-239">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="c4e5e-240">**Sslcertificatekeyfile** , CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-240">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="c4e5e-241">**Sslcertificatechainfile** , sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-241">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="c4e5e-242">Dosyayı kaydedin ve yapılandırmayı test edin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-242">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="c4e5e-243">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-243">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="c4e5e-244">Ek Apache önerileri</span><span class="sxs-lookup"><span data-stu-id="c4e5e-244">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="c4e5e-245">Paylaşılan çerçeve güncelleştirmeleriyle uygulamaları yeniden başlatma</span><span class="sxs-lookup"><span data-stu-id="c4e5e-245">Restart apps with shared framework updates</span></span>

<span data-ttu-id="c4e5e-246">Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-246">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="c4e5e-247">Ek üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="c4e5e-247">Additional headers</span></span>

<span data-ttu-id="c4e5e-248">Kötü amaçlı saldırılara karşı korumak için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-248">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="c4e5e-249">`mod_headers` Modülün yüklü olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-249">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="c4e5e-250">Tıklama ve tıklama saldırılarına karşı güvenli Apache</span><span class="sxs-lookup"><span data-stu-id="c4e5e-250">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="c4e5e-251">*UI redki saldırısı*olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="c4e5e-252">Sitesini `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-252">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="c4e5e-253">Tıklama saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-253">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="c4e5e-254">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-254">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="c4e5e-255">`Header append X-FRAME-OPTIONS "SAMEORIGIN"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-255">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="c4e5e-256">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-256">Save the file.</span></span>
1. <span data-ttu-id="c4e5e-257">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-257">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="c4e5e-258">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="c4e5e-258">MIME-type sniffing</span></span>

<span data-ttu-id="c4e5e-259">Üst `X-Content-Type-Options` bilgi, Internet Explorer 'ın *MIME algılaması* (dosyanın içeriğinden bir dosya `Content-Type` belirleme) gerçekleştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-259">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="c4e5e-260">Sunucu `Content-Type` üstbilgiyi `text/html` `nosniff` seçenek kümesiyle ayarlarsa, Internet Explorer içeriği dosyanın içeriğinden bağımsız olarak `text/html` işler.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-260">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="c4e5e-261">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-261">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="c4e5e-262">`Header set X-Content-Type-Options "nosniff"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-262">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="c4e5e-263">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-263">Save the file.</span></span> <span data-ttu-id="c4e5e-264">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-264">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="c4e5e-265">Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="c4e5e-265">Load Balancing</span></span>

<span data-ttu-id="c4e5e-266">Bu örnek, CentOS 7 ve Kestrel üzerinde Apache 'in aynı örnek makinede nasıl ayarlanacağını ve yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-266">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="c4e5e-267">Tek bir hata noktası olmaması için; *mod_proxy_balancer* kullanmak ve **VirtualHost** 'u değiştirmek, Apache proxy sunucusunun arkasındaki Web uygulamalarının birden çok örneğini yönetmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-267">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="c4e5e-268">Aşağıda gösterilen yapılandırma dosyasında, bağlantı noktası 5001 ' de çalışacak ek `helloapp` bir örneği ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-268">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="c4e5e-269">*Proxy* bölümü, Yük Dengeleme *istekleri*için iki üyeli bir dengeleyici yapılandırması ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-269">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="c4e5e-270">Oran limitleri</span><span class="sxs-lookup"><span data-stu-id="c4e5e-270">Rate Limits</span></span>

<span data-ttu-id="c4e5e-271">*Httpd* modülüne eklenen *mod_ratelimit*kullanarak, istemcilerin bant genişliği sınırlandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-271">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="c4e5e-272">Örnek dosya, bant genişliğini kök konumu altında 600 KB/sn olarak sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="c4e5e-272">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="c4e5e-273">Uzun istek üst bilgisi alanları</span><span class="sxs-lookup"><span data-stu-id="c4e5e-273">Long request header fields</span></span>

<span data-ttu-id="c4e5e-274">Proxy sunucusu varsayılan ayarları, istek üst bilgisi alanlarını genellikle 8.190 bayt ile sınırlar.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-274">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="c4e5e-275">Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="c4e5e-275">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="c4e5e-276">Daha uzun alanlar gerekliyse, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlamayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-276">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="c4e5e-277">Uygulanacak değer senaryoya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-277">The value to apply depends on the scenario.</span></span> <span data-ttu-id="c4e5e-278">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-278">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="c4e5e-279">Gerekli `LimitRequestFieldSize` olmadığı takdirde varsayılan değerini artırmaz.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-279">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="c4e5e-280">Değerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="c4e5e-280">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4e5e-281">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c4e5e-281">Additional resources</span></span>

* [<span data-ttu-id="c4e5e-282">Linux üzerinde .NET Core önkoşulları</span><span class="sxs-lookup"><span data-stu-id="c4e5e-282">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
