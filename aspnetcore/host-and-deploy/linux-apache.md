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
ms.openlocfilehash: e81ad43e1c3b86900848671d9da377a5c04a2a82
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253013"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="1a6b5-103">Apache ile Linux üzerinde ASP.NET Core barındırma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="1a6b5-104">Sağlayan- [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="1a6b5-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="1a6b5-105">Bu kılavuzu kullanarak, HTTP trafiğinin [Kestrel](xref:fundamentals/servers/kestrel) Server üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirilmesini sağlamak Için [CentOS 7](https://www.centos.org/) ' de bir ters proxy sunucusu olarak [Apache](https://httpd.apache.org/) 'yi ayarlamayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="1a6b5-106">[Mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili Modüller sunucunun ters proxy 'sini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a6b5-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1a6b5-107">Prerequisites</span></span>

* <span data-ttu-id="1a6b5-108">Sudo ayrıcalığına sahip standart bir kullanıcı hesabıyla CentOS 7 çalıştıran sunucu.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="1a6b5-109">.NET Core çalışma zamanını sunucuya yükler.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="1a6b5-110">[.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="1a6b5-111">En son Önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="1a6b5-112">**Uygulama çalıştırma-çalışma zamanı** altındaki tabloda en son önizleme dışı çalışma zamanını indirin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="1a6b5-113">Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve CentOS talimatlarını izleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="1a6b5-114">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="1a6b5-115">Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="1a6b5-116">Uygulama üzerinde Yayımla ve Kopyala</span><span class="sxs-lookup"><span data-stu-id="1a6b5-116">Publish and copy over the app</span></span>

<span data-ttu-id="1a6b5-117">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="1a6b5-118">Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="1a6b5-119">Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="1a6b5-120">Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="1a6b5-121">`https://localhost:5001` `applicationUrl` Dosyadaki *Properties/launchSettings.js* özelliğinden (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="1a6b5-122">Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/ &lt; target_framework_moniker &gt; /Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="1a6b5-123">Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="1a6b5-124">ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="1a6b5-125">*Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="1a6b5-126">Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="1a6b5-127">Proxy sunucusu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-127">Configure a proxy server</span></span>

<span data-ttu-id="1a6b5-128">Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="1a6b5-129">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="1a6b5-130">Proxy sunucusu, istemci isteklerini istekleri yerine başka bir sunucuya iletir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-130">A proxy server forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="1a6b5-131">Ters proxy, genellikle rastgele istemciler adına sabit bir hedefe iletilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="1a6b5-132">Bu kılavuzda Apache, Kestrel 'in ASP.NET Core uygulamasına hizmet veren aynı sunucuda çalışan ters proxy olarak yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="1a6b5-133">İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="1a6b5-134">Ara yazılım, `Request.Scheme` üstbilgiyi kullanarak, `X-Forwarded-Proto` yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="1a6b5-135">Bir şemaya bağlı kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirme ve coğrafi konum gibi herhangi bir bileşen, Iletilen üstbilgiler ara yazılımı çağrıldıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="1a6b5-136"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> `Startup.Configure` Diğer ara yazılım çağrılmadan önce yönteminin en üstünde yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="1a6b5-137">Ara yazılımı, `X-Forwarded-For` ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="1a6b5-138">Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="1a6b5-139">`127.0.0.0/8, [::1]`Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler, varsayılan olarak güvenilirdir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-139">Proxies running on loopback addresses (`127.0.0.0/8, [::1]`), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="1a6b5-140">Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> veya ile listesine ekleyin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="1a6b5-141">Aşağıdaki örnek, içindeki Iletilen üstbilgiler ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler `KnownProxies` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="1a6b5-142">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="1a6b5-143">Apache 'yi yükler</span><span class="sxs-lookup"><span data-stu-id="1a6b5-143">Install Apache</span></span>

<span data-ttu-id="1a6b5-144">CentOS paketlerini en son kararlı sürümlerine güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="1a6b5-145">Tek bir komutla, CentOS üzerinde Apache Web sunucusunu yükler `yum` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="1a6b5-146">Komutu çalıştırdıktan sonra örnek çıkış:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-146">Sample output after running the command:</span></span>

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
> <span data-ttu-id="1a6b5-147">Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıkış httpd.86_64 yansıtır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="1a6b5-148">Apache 'nin yüklü olduğu yeri doğrulamak için `whereis httpd` komut isteminden komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="1a6b5-149">Apache yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-149">Configure Apache</span></span>

<span data-ttu-id="1a6b5-150">Apache için yapılandırma dosyaları, dizin içinde bulunur `/etc/httpd/conf.d/` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="1a6b5-151">*. Conf* uzantısına sahip herhangi bir dosya, içindeki modül yapılandırma dosyalarının yanı sıra `/etc/httpd/conf.modules.d/` , modülleri yüklemek için gereken yapılandırma dosyalarını içeren alfabetik sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="1a6b5-152">Uygulama için *HelloApp. conf* adlı bir yapılandırma dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1a6b5-153">`VirtualHost`Blok, sunucuda bir veya daha fazla dosyada birden çok kez görünebilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="1a6b5-154">Yukarıdaki yapılandırma dosyasında Apache, 80 numaralı bağlantı noktasında genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="1a6b5-155">Etki alanına `www.example.com` sunulmakta ve `*.example.com` diğer ad aynı Web sitesine çözümlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="1a6b5-156">Daha fazla bilgi için bkz. [ad tabanlı sanal konak desteği](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-156">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="1a6b5-157">İstekler, kökte, sunucunun bağlantı noktası 5000 ' den 127.0.0.1 ' de sunucu üzerinden alınır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="1a6b5-158">İki yönlü iletişim için `ProxyPass` `ProxyPassReverse` gereklidir ve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="1a6b5-159">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1a6b5-160">`VirtualHost`Blok, sunucuda bir veya daha fazla dosyada birden çok kez görünebilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-160">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="1a6b5-161">Yukarıdaki yapılandırma dosyasında Apache, 80 numaralı bağlantı noktasında genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-161">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="1a6b5-162">Etki alanına `www.example.com` sunulmakta ve `*.example.com` diğer ad aynı Web sitesine çözümlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-162">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="1a6b5-163">Daha fazla bilgi için bkz. [ad tabanlı sanal konak desteği](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-163">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="1a6b5-164">İstekler, kökte, sunucunun bağlantı noktası 5000 ' den 127.0.0.1 ' de sunucu üzerinden alınır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-164">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="1a6b5-165">İki yönlü iletişim için `ProxyPass` `ProxyPassReverse` gereklidir ve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-165">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="1a6b5-166">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-166">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="1a6b5-167">**VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-167">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="1a6b5-168">Alt etki alanı joker karakteri bağlama (örneğin, `*.example.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz `*.com` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-168">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="1a6b5-169">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-169">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="1a6b5-170">Günlüğe kaydetme, `VirtualHost` `ErrorLog` ve yönergeleri başına yapılandırılabilir `CustomLog` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-170">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="1a6b5-171">`ErrorLog` , sunucunun hataları günlüğe kaydettiği konumdur ve `CustomLog` günlük dosyasının dosya adını ve biçimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-171">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="1a6b5-172">Bu durumda istek bilgileri günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-172">In this case, this is where request information is logged.</span></span> <span data-ttu-id="1a6b5-173">Her istek için bir satır vardır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-173">There's one line for each request.</span></span>

<span data-ttu-id="1a6b5-174">Dosyayı kaydedin ve yapılandırmayı test edin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-174">Save the file and test the configuration.</span></span> <span data-ttu-id="1a6b5-175">Her şey geçerse yanıtın olması gerekir `Syntax [OK]` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-175">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="1a6b5-176">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-176">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="1a6b5-177">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="1a6b5-177">Monitor the app</span></span>

<span data-ttu-id="1a6b5-178">Apache, `http://localhost:80` ' de Kestrel üzerinde çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek üzere ayarlanmıştır `http://127.0.0.1:5000` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-178">Apache is now set up to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="1a6b5-179">Ancak, Kestrel işlemini yönetmek için Apache ayarlanmamış.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-179">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="1a6b5-180">Temel Web uygulamasını başlatmak ve izlemek için *systemd* ve hizmet dosyası oluşturma ' yı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-180">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="1a6b5-181">*systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-181">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="1a6b5-182">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-182">Create the service file</span></span>

<span data-ttu-id="1a6b5-183">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-183">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="1a6b5-184">Uygulama için örnek bir hizmet dosyası:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-184">An example service file for the app:</span></span>

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

<span data-ttu-id="1a6b5-185">Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-185">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="1a6b5-186">Kullanıcı ( `apache` ) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-186">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="1a6b5-187">`TimeoutStopSec`Uygulamanın ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-187">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="1a6b5-188">Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-188">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="1a6b5-189">Değeri unitless saniyeler (örneğin, `150` ), bir zaman aralığı değeri (örneğin, `2min 30s` ) veya `infinity` zaman aşımını devre dışı bırakmak için girin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-189">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="1a6b5-190">`TimeoutStopSec` Varsayılan olarak, `DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değerini alır (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-190">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="1a6b5-191">Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-191">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="1a6b5-192">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-192">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="1a6b5-193">Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-193">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a6b5-194">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-194">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="1a6b5-195">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-195">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="1a6b5-196">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-196">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="1a6b5-197">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-197">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a6b5-198">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-198">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="1a6b5-199">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-199">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="1a6b5-200">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-200">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="1a6b5-201">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-201">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="1a6b5-202">Dosyayı kaydedin ve hizmeti etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-202">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="1a6b5-203">Hizmeti başlatın ve çalıştığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-203">Start the service and verify that it's running:</span></span>

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

<span data-ttu-id="1a6b5-204">Ters proxy yapılandırılmış ve *systemd* üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden yerel makinedeki bir tarayıcıdan erişilebilir `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-204">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="1a6b5-205">Yanıt üst bilgilerini inceleyerek **sunucu** üst bilgisi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-205">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="1a6b5-206">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="1a6b5-206">View logs</span></span>

<span data-ttu-id="1a6b5-207">Kestrel kullanan Web uygulaması *systemd* kullanılarak yönetildiğinden, olaylar ve süreçler merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-207">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="1a6b5-208">Ancak, bu günlük *systemd* tarafından yönetilen tüm hizmet ve işlemlere ait girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-208">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="1a6b5-209">`kestrel-helloapp.service`Belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-209">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="1a6b5-210">Zaman filtreleme için komutuyla saat seçeneklerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-210">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="1a6b5-211">Örneğin, `--since today` geçerli güne filtre uygulamak veya `--until 1 hour ago` önceki saatin girişlerini görmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-211">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="1a6b5-212">Daha fazla bilgi için bkz. [journalctl için man sayfası](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-212">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="1a6b5-213">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-213">Data protection</span></span>

<span data-ttu-id="1a6b5-214">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı ( [](xref:fundamentals/middleware/index)Örneğin, cookie Ara yazılım) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core middlewares tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-214">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="1a6b5-215">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-215">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="1a6b5-216">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-216">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="1a6b5-217">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-217">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="1a6b5-218">Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-218">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="1a6b5-219">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-219">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="1a6b5-220">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-220">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="1a6b5-221">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-221">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="1a6b5-222">Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-222">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="1a6b5-223">Uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="1a6b5-223">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="1a6b5-224">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-224">Configure firewall</span></span>

<span data-ttu-id="1a6b5-225">*Firewallld* , ağ bölgeleri desteğiyle güvenlik duvarını yönetmek için dinamik bir Daemon.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-225">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="1a6b5-226">Bağlantı noktaları ve paket filtrelemesi, Iptables tarafından hala yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-226">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="1a6b5-227">*Firewalld* , varsayılan olarak yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-227">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="1a6b5-228">`yum` paketi yüklemek veya yüklendiğini doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-228">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="1a6b5-229">`firewalld`Yalnızca uygulama için gerekli olan bağlantı noktalarını açmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-229">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="1a6b5-230">Bu durumda, 80 ve 443 bağlantı noktaları kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-230">In this case, ports 80 and 443 are used.</span></span> <span data-ttu-id="1a6b5-231">Aşağıdaki komutlar şunları açmak için 80 ve 443 bağlantı noktalarını kalıcı olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-231">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="1a6b5-232">Güvenlik Duvarı ayarlarını yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-232">Reload the firewall settings.</span></span> <span data-ttu-id="1a6b5-233">Varsayılan bölgedeki kullanılabilir hizmetleri ve bağlantı noktalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-233">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="1a6b5-234">Seçenekler inceleyerek kullanılabilir `firewall-cmd -h` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-234">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="1a6b5-235">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="1a6b5-235">HTTPS configuration</span></span>

<span data-ttu-id="1a6b5-236">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="1a6b5-236">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="1a6b5-237">[DotNet Run](/dotnet/core/tools/dotnet-run) komutu, uygulamayı özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandıran, uygulamanın *Özellikler/launchSettings.js* kullanır `applicationUrl` (örneğin, `https://localhost:5001;http://localhost:5000` ).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-237">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="1a6b5-238">`dotnet run`Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + f5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-238">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="1a6b5-239">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel/endpoints#configuration) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="1a6b5-239">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="1a6b5-240">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="1a6b5-240">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="1a6b5-241">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="1a6b5-241">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="1a6b5-242">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="1a6b5-242">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="1a6b5-243">**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="1a6b5-243">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="1a6b5-244">HTTPS için Apache 'yi yapılandırmak için *mod_ssl* modülü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-244">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="1a6b5-245">*Httpd* modülü yüklendiğinde *mod_ssl* modülü de yüklendi.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-245">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="1a6b5-246">Yüklenmemişse, `yum` yapılandırmaya eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-246">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="1a6b5-247">HTTPS 'yi zorlamak için, `mod_rewrite` URL yeniden yazmayı etkinleştirmek üzere modülünü yükler:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-247">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="1a6b5-248">Bağlantı noktası 443 üzerinde URL yeniden yazma ve güvenli iletişim sağlamak için *HelloApp. conf* dosyasını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-248">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="1a6b5-249">Bu örnek, yerel olarak üretilmiş bir sertifika kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-249">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="1a6b5-250">**Sslcertificatefile** , etki alanı adı için birincil sertifika dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-250">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="1a6b5-251">**Sslcertificatekeyfile** , CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-251">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="1a6b5-252">**Sslcertificatechainfile** , sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-252">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="1a6b5-253">Dosyayı kaydedin ve yapılandırmayı test edin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-253">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="1a6b5-254">Apache 'i yeniden Başlat:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-254">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="1a6b5-255">Ek Apache önerileri</span><span class="sxs-lookup"><span data-stu-id="1a6b5-255">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="1a6b5-256">Paylaşılan çerçeve güncelleştirmeleriyle uygulamaları yeniden başlatma</span><span class="sxs-lookup"><span data-stu-id="1a6b5-256">Restart apps with shared framework updates</span></span>

<span data-ttu-id="1a6b5-257">Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-257">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="1a6b5-258">Ek üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="1a6b5-258">Additional headers</span></span>

<span data-ttu-id="1a6b5-259">Kötü amaçlı saldırılara karşı korumak için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-259">To secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="1a6b5-260">Modülün yüklü olduğundan emin olun `mod_headers` :</span><span class="sxs-lookup"><span data-stu-id="1a6b5-260">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="1a6b5-261">Tıklama ve tıklama saldırılarına karşı güvenli Apache</span><span class="sxs-lookup"><span data-stu-id="1a6b5-261">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="1a6b5-262">*UI redki saldırısı* olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-262">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="1a6b5-263">`X-FRAME-OPTIONS`Sitesini güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-263">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="1a6b5-264">Tıklama saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-264">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="1a6b5-265">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-265">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="1a6b5-266">`Header append X-FRAME-OPTIONS "SAMEORIGIN"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-266">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="1a6b5-267">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-267">Save the file.</span></span>
1. <span data-ttu-id="1a6b5-268">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-268">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="1a6b5-269">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="1a6b5-269">MIME-type sniffing</span></span>

<span data-ttu-id="1a6b5-270">`X-Content-Type-Options`Üst bilgi, Internet Explorer 'ın *MIME algılaması* (dosyanın içeriğinden bir dosya belirleme `Content-Type` ) gerçekleştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-270">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="1a6b5-271">Sunucu `Content-Type` üstbilgiyi `text/html` seçenek kümesiyle ayarlarsa `nosniff` , Internet Explorer içeriği `text/html` dosyanın içeriğinden bağımsız olarak işler.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-271">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="1a6b5-272">*Httpd. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-272">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="1a6b5-273">`Header set X-Content-Type-Options "nosniff"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-273">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="1a6b5-274">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-274">Save the file.</span></span> <span data-ttu-id="1a6b5-275">Apache 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-275">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="1a6b5-276">YükDengeleme</span><span class="sxs-lookup"><span data-stu-id="1a6b5-276">Load Balancing</span></span>

<span data-ttu-id="1a6b5-277">Bu örnek, CentOS 7 ve Kestrel üzerinde Apache 'in aynı örnek makinede nasıl ayarlanacağını ve yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-277">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="1a6b5-278">Tek bir hata noktası yok; *mod_proxy_balancer* kullanmak ve **VirtualHost** 'u değiştirmek, Apache proxy sunucusunun arkasındaki Web uygulamalarının birden çok örneğini yönetmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-278">To not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="1a6b5-279">Aşağıda gösterilen yapılandırma dosyasında, bağlantı noktası 5001 ' de çalışacak ek bir örneği `helloapp` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-279">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="1a6b5-280">*Proxy* bölümü, Yük Dengeleme *istekleri* için iki üyeli bir dengeleyici yapılandırması ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-280">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="1a6b5-281">Oran limitleri</span><span class="sxs-lookup"><span data-stu-id="1a6b5-281">Rate Limits</span></span>

<span data-ttu-id="1a6b5-282">*Httpd* modülüne eklenen *mod_ratelimit* kullanarak, istemcilerin bant genişliği sınırlandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-282">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="1a6b5-283">Örnek dosya, bant genişliğini kök konumu altında 600 KB/sn olarak sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="1a6b5-283">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="1a6b5-284">Uzun istek üst bilgisi alanları</span><span class="sxs-lookup"><span data-stu-id="1a6b5-284">Long request header fields</span></span>

<span data-ttu-id="1a6b5-285">Proxy sunucusu varsayılan ayarları, istek üst bilgisi alanlarını genellikle 8.190 bayt ile sınırlar.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-285">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="1a6b5-286">Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="1a6b5-286">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="1a6b5-287">Daha uzun alanlar gerekliyse, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlamayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-287">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="1a6b5-288">Uygulanacak değer senaryoya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-288">The value to apply depends on the scenario.</span></span> <span data-ttu-id="1a6b5-289">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-289">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="1a6b5-290">Gerekli olmadığı takdirde varsayılan değerini artırmaz `LimitRequestFieldSize` .</span><span class="sxs-lookup"><span data-stu-id="1a6b5-290">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="1a6b5-291">Değerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="1a6b5-291">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a6b5-292">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1a6b5-292">Additional resources</span></span>

* [<span data-ttu-id="1a6b5-293">Linux üzerinde .NET Core önkoşulları</span><span class="sxs-lookup"><span data-stu-id="1a6b5-293">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
