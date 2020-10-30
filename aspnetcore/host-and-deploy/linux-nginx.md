---
title: NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core
author: rick-anderson
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına iletmek için Ubuntu 16,04 üzerinde ters proxy olarak NGINX 'i ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 916bb1f761ce99b2296c84e1653e55fffa04f83c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057693"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="53674-103">NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53674-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="53674-104">, [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="53674-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="53674-105">Bu kılavuzda Ubuntu 16,04 sunucusunda üretime hazır ASP.NET Core ortamı ayarlama açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="53674-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="53674-106">Bu yönergeler büyük olasılıkla Ubuntu 'ın daha yeni sürümleriyle çalışır, ancak yönergeler daha yeni sürümlerle sınanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="53674-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="53674-107">ASP.NET Core tarafından desteklenen diğer Linux dağıtımları hakkında daha fazla bilgi için bkz. [Linux üzerinde .NET Core önkoşulları](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="53674-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="53674-108">Ubuntu 14,04 için, Kestrel işlemini izlemeye yönelik bir çözüm olarak *supervisof* önerilir.</span><span class="sxs-lookup"><span data-stu-id="53674-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="53674-109">*systemd* , Ubuntu 14,04 ' de kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="53674-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="53674-110">Ubuntu 14,04 yönergeleri için [Bu konunun önceki sürümüne](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="53674-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="53674-111">Bu kılavuz:</span><span class="sxs-lookup"><span data-stu-id="53674-111">This guide:</span></span>

* <span data-ttu-id="53674-112">Mevcut bir ASP.NET Core uygulamasını bir ters proxy sunucusunun arkasına koyar.</span><span class="sxs-lookup"><span data-stu-id="53674-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="53674-113">İstekleri Kestrel Web sunucusuna iletmek için ters proxy sunucusunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="53674-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="53674-114">Web uygulamasının, bir arka plan programı olarak başlangıcında çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="53674-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="53674-115">Web uygulamasını yeniden başlatmanıza yardımcı olması için bir işlem yönetim aracı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="53674-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="53674-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="53674-116">Prerequisites</span></span>

1. <span data-ttu-id="53674-117">Sudo ayrıcalığına sahip standart bir kullanıcı hesabı ile Ubuntu 16,04 sunucusuna erişim.</span><span class="sxs-lookup"><span data-stu-id="53674-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="53674-118">.NET Core çalışma zamanını sunucuya yükler.</span><span class="sxs-lookup"><span data-stu-id="53674-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="53674-119">[.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="53674-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="53674-120">En son Önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="53674-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="53674-121">**Uygulama çalıştırma-çalışma zamanı** altındaki tabloda en son önizleme dışı çalışma zamanını indirin.</span><span class="sxs-lookup"><span data-stu-id="53674-121">Download the latest non-preview runtime in the table under **Run apps - Runtime** .</span></span>
   1. <span data-ttu-id="53674-122">Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve Ubuntu sürümünüz Için Ubuntu yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="53674-123">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="53674-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="53674-124">Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="53674-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="53674-125">Uygulama üzerinde Yayımla ve Kopyala</span><span class="sxs-lookup"><span data-stu-id="53674-125">Publish and copy over the app</span></span>

<span data-ttu-id="53674-126">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="53674-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="53674-127">Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="53674-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="53674-128">Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="53674-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="53674-129">Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="53674-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="53674-130">`https://localhost:5001` `applicationUrl` Dosyadaki *Properties/launchSettings.js* özelliğinden (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="53674-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="53674-131">Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/ &lt; target_framework_moniker &gt; /Publish* ) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="53674-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="53674-132">Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="53674-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="53674-133">ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="53674-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="53674-134">*Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp* ) yerini bulmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="53674-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp* ).</span></span>

> [!NOTE]
> <span data-ttu-id="53674-135">Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="53674-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="53674-136">Uygulamayı test etme:</span><span class="sxs-lookup"><span data-stu-id="53674-136">Test the app:</span></span>

1. <span data-ttu-id="53674-137">Komut satırından uygulamayı çalıştırın: `dotnet <app_assembly>.dll` .</span><span class="sxs-lookup"><span data-stu-id="53674-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="53674-138">Bir tarayıcıda, `http://<serveraddress>:<port>` uygulamanın Linux üzerinde yerel olarak çalıştığını doğrulamak için bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="53674-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="53674-139">Ters proxy sunucu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="53674-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="53674-140">Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir.</span><span class="sxs-lookup"><span data-stu-id="53674-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="53674-141">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET Core uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="53674-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="53674-142">Ters proxy sunucusu kullanma</span><span class="sxs-lookup"><span data-stu-id="53674-142">Use a reverse proxy server</span></span>

<span data-ttu-id="53674-143">Kestrel, ASP.NET Core ' den dinamik içerik sunmak için harika.</span><span class="sxs-lookup"><span data-stu-id="53674-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="53674-144">Ancak, Web 'e sunma özellikleri IIS, Apache veya NGINX gibi sunucu olarak zengin özellik değildir.</span><span class="sxs-lookup"><span data-stu-id="53674-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="53674-145">Ters bir ara sunucu, statik içerik sunma, istekleri önbelleğe alma, istekleri sıkıştırma ve HTTP sunucusundan HTTPS sonlandırması gibi işleri devreedebilir.</span><span class="sxs-lookup"><span data-stu-id="53674-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="53674-146">Ters ara sunucu, ayrılmış bir makinede bulunabilir veya bir HTTP sunucusu ile birlikte dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="53674-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="53674-147">Bu kılavuzun amaçları doğrultusunda, tek bir NGINX örneği kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="53674-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="53674-148">Bu, HTTP sunucusu ile birlikte aynı sunucuda çalışır.</span><span class="sxs-lookup"><span data-stu-id="53674-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="53674-149">Gereksinimlere bağlı olarak, farklı bir kurulum seçilebilir.</span><span class="sxs-lookup"><span data-stu-id="53674-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="53674-150">İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="53674-151">Ara yazılım, `Request.Scheme` üstbilgiyi kullanarak, `X-Forwarded-Proto` yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="53674-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="53674-152"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce yönteminin en üstünde yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="53674-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="53674-153">Ara yazılımı, `X-Forwarded-For` ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` :</span><span class="sxs-lookup"><span data-stu-id="53674-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="53674-154">Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None` .</span><span class="sxs-lookup"><span data-stu-id="53674-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="53674-155">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir.</span><span class="sxs-lookup"><span data-stu-id="53674-155">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="53674-156">Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya ile listesine ekleyin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> .</span><span class="sxs-lookup"><span data-stu-id="53674-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="53674-157">Aşağıdaki örnek, içindeki Iletilen üstbilgiler ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler `KnownProxies` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="53674-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="53674-158">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="53674-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="53674-159">NGINX 'i yükler</span><span class="sxs-lookup"><span data-stu-id="53674-159">Install Nginx</span></span>

<span data-ttu-id="53674-160">`apt-get`NGINX yüklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="53674-161">Yükleyici, sistem başlangıcında arka plan programı olarak NGINX çalıştıran bir *systemd* init betiği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="53674-161">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="53674-162">[NGINX: resmi deni/Ubuntu paketlerinde](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)Ubuntu yükleme yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="53674-163">İsteğe bağlı NGINX modülleri gerekliyse, kaynaktan NGINX oluşturulması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="53674-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="53674-164">NGINX ilk kez yüklendiğinden bu yana şunu çalıştırarak açık olarak başlatın:</span><span class="sxs-lookup"><span data-stu-id="53674-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="53674-165">Bir tarayıcının NGINX için varsayılan giriş sayfasını görüntülediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="53674-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="53674-166">Giriş sayfasına adresinden ulaşılabilir `http://<server_IP_address>/index.nginx-debian.html` .</span><span class="sxs-lookup"><span data-stu-id="53674-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="53674-167">Nginx hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="53674-167">Configure Nginx</span></span>

<span data-ttu-id="53674-168">İstekleri ASP.NET Core uygulamanıza iletmek için NGINX 'i ters proxy olarak yapılandırmak için */etc/nginx/sites-available/default* değiştirin.</span><span class="sxs-lookup"><span data-stu-id="53674-168">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default* .</span></span> <span data-ttu-id="53674-169">Bu dosyayı bir metin düzenleyicisinde açın ve içeriği şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="53674-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="53674-170">Uygulama bir :::no-loc(SignalR)::: veya :::no-loc(Blazor Server)::: uygulama ise <xref:signalr/scale#linux-with-nginx> <xref:blazor/host-and-deploy/server#linux-with-nginx> daha fazla bilgi için bkz..</span><span class="sxs-lookup"><span data-stu-id="53674-170">If the app is a :::no-loc(SignalR)::: or :::no-loc(Blazor Server)::: app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="53674-171">Hiçbir `server_name` eşleşme olmadığında NGINX varsayılan sunucuyu kullanır.</span><span class="sxs-lookup"><span data-stu-id="53674-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="53674-172">Varsayılan sunucu tanımlanmazsa, yapılandırma dosyasındaki ilk sunucu varsayılan sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="53674-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="53674-173">En iyi uygulama olarak, yapılandırma dosyanızda 444 durum kodunu döndüren belirli bir varsayılan sunucu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="53674-174">Varsayılan bir sunucu yapılandırma örneği:</span><span class="sxs-lookup"><span data-stu-id="53674-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="53674-175">Önceki yapılandırma dosyası ve varsayılan sunucu ile NGINX, bağlantı noktası 80 üzerinde ana bilgisayar üst bilgisi veya olan genel trafiği kabul eder `example.com` `*.example.com` .</span><span class="sxs-lookup"><span data-stu-id="53674-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="53674-176">Bu konaklarla eşleşmeyen istekler Kestrel 'e iletilemiyor.</span><span class="sxs-lookup"><span data-stu-id="53674-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="53674-177">NGINX eşleşen istekleri şurada Kestrel 'e iletir `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="53674-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="53674-178">Daha fazla bilgi için [NGINX 'in Isteği nasıl işliyorsa öğrenin](https://nginx.org/docs/http/request_processing.html) .</span><span class="sxs-lookup"><span data-stu-id="53674-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="53674-179">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="53674-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="53674-180">Uygun bir [SERVER_NAME yönergesi](https://nginx.org/docs/http/server_names.html) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="53674-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="53674-181">Alt etki alanı joker karakteri bağlama (örneğin, `*.example.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz `*.com` .</span><span class="sxs-lookup"><span data-stu-id="53674-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="53674-182">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="53674-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="53674-183">NGINX yapılandırması kurulduktan sonra, `sudo nginx -t` yapılandırma dosyalarının söz dizimini doğrulamak için ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="53674-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="53674-184">Yapılandırma dosyası testi başarılıysa, NGINX ' i çalıştırarak değişiklikleri çekmeye zorlayın `sudo nginx -s reload` .</span><span class="sxs-lookup"><span data-stu-id="53674-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="53674-185">Uygulamayı sunucuda doğrudan çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="53674-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="53674-186">Uygulamanın dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="53674-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="53674-187">Uygulamayı çalıştırın: `dotnet <app_assembly.dll>` , burada `app_assembly.dll` uygulamanın derleme dosyası adıdır.</span><span class="sxs-lookup"><span data-stu-id="53674-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="53674-188">Uygulama sunucuda çalışır, ancak Internet üzerinden yanıt vermezse, sunucunun güvenlik duvarını denetleyin ve 80 bağlantı noktasının açık olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="53674-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="53674-189">Azure Ubuntu VM kullanıyorsanız, gelen bağlantı noktası 80 trafiğine izin veren bir ağ güvenlik grubu (NSG) kuralı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="53674-190">Giden trafik, gelen kuralı etkinleştirildiğinde otomatik olarak verildiği için, giden bağlantı noktası 80 kuralını etkinleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="53674-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="53674-191">Uygulamayı test etmeyi tamamladıktan sonra komut isteminde uygulamayı ile kapatın `Ctrl+C` .</span><span class="sxs-lookup"><span data-stu-id="53674-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="53674-192">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="53674-192">Monitor the app</span></span>

<span data-ttu-id="53674-193">Sunucu, üzerinde yapılan istekleri `http://<serveraddress>:80` üzerinde Kestrel üzerinde çalışan ASP.NET Core uygulamasına iletmek üzere ayarlanır `http://127.0.0.1:5000` .</span><span class="sxs-lookup"><span data-stu-id="53674-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="53674-194">Ancak, NGINX Kestrel işlemini yönetmek için ayarlanmadı.</span><span class="sxs-lookup"><span data-stu-id="53674-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="53674-195">*systemd* , temel Web uygulamasını başlatmak ve izlemek üzere bir hizmet dosyası oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="53674-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="53674-196">*systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="53674-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="53674-197">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="53674-197">Create the service file</span></span>

<span data-ttu-id="53674-198">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="53674-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="53674-199">Aşağıda, uygulama için örnek bir hizmet dosyası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="53674-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="53674-200">Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="53674-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="53674-201">Kullanıcı ( `www-data` ) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.</span><span class="sxs-lookup"><span data-stu-id="53674-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="53674-202">`TimeoutStopSec`Uygulamanın ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="53674-203">Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="53674-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="53674-204">Değeri unitless saniyeler (örneğin, `150` ), bir zaman aralığı değeri (örneğin, `2min 30s` ) veya `infinity` zaman aşımını devre dışı bırakmak için girin.</span><span class="sxs-lookup"><span data-stu-id="53674-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="53674-205">`TimeoutStopSec` Varsayılan olarak, `DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değerini alır ( *systemd-System. conf* , *System. conf. d* , *systemd-User. conf* , *User. conf. d* ).</span><span class="sxs-lookup"><span data-stu-id="53674-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span></span> <span data-ttu-id="53674-206">Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="53674-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="53674-207">Linux, büyük/küçük harfe duyarlı bir dosya sistemine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="53674-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="53674-208">ASPNETCORE_ENVIRONMENT "üretim" olarak ayarlamak, üzerinde *appsettings.production.js* değil, *appsettings.Production.js* yapılandırma dosyasını aramaya neden olur.</span><span class="sxs-lookup"><span data-stu-id="53674-208">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json* , not *appsettings.production.json* .</span></span>

<span data-ttu-id="53674-209">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="53674-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="53674-210">Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="53674-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="53674-211">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="53674-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="53674-212">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="53674-213">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="53674-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="53674-214">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="53674-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53674-215">İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="53674-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="53674-216">İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="53674-217">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="53674-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="53674-218">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="53674-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="53674-219">Dosyayı kaydedin ve hizmeti etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="53674-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="53674-220">Hizmeti başlatın ve çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="53674-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="53674-221">Ters proxy yapılandırılmış ve systemd üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden yerel makinedeki bir tarayıcıdan erişilebilir `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="53674-221">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="53674-222">Ayrıca, uzak bir makineden de erişilebilir, engelleyici olabilecek tüm güvenlik duvarını açabilir.</span><span class="sxs-lookup"><span data-stu-id="53674-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="53674-223">Yanıt üst bilgilerini inceleyerek `Server` üst bilgi, Kestrel tarafından sunulan ASP.NET Core uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="53674-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="53674-224">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="53674-224">View logs</span></span>

<span data-ttu-id="53674-225">Kestrel kullanan Web uygulaması kullanılarak yönetildiğinden `systemd` , tüm olaylar ve süreçler merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="53674-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="53674-226">Ancak bu günlük, tarafından yönetilen tüm hizmetler ve süreçler için tüm girişleri içerir `systemd` .</span><span class="sxs-lookup"><span data-stu-id="53674-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="53674-227">`kestrel-helloapp.service`Belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="53674-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="53674-228">Daha fazla filtreleme için, gibi zaman seçenekleri `--since today` `--until 1 hour ago` veya bunların bir birleşimi döndürülen girdi miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="53674-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="53674-229">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="53674-229">Data protection</span></span>

<span data-ttu-id="53674-230">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı ( [middlewares](xref:fundamentals/middleware/index)Örneğin, :::no-loc(cookie)::: Ara yazılım) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core middlewares tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="53674-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, :::no-loc(cookie)::: middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="53674-231">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="53674-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="53674-232">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="53674-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="53674-233">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="53674-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="53674-234">Tüm :::no-loc(cookie)::: tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="53674-234">All :::no-loc(cookie):::-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="53674-235">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="53674-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="53674-236">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="53674-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="53674-237">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData :::no-loc(cookie)::: s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="53674-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData :::no-loc(cookie):::s](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="53674-238">Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:</span><span class="sxs-lookup"><span data-stu-id="53674-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="53674-239">Uzun istek üst bilgisi alanları</span><span class="sxs-lookup"><span data-stu-id="53674-239">Long request header fields</span></span>

<span data-ttu-id="53674-240">Proxy sunucusu varsayılan ayarları, platforma bağlı olarak genellikle istek üst bilgisi alanlarını 4 K veya 8 K ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="53674-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="53674-241">Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="53674-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="53674-242">Daha uzun alanlar gerekliyse, proxy sunucusunun varsayılan ayarları ayarlama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="53674-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="53674-243">Uygulanacak değerler senaryoya göre değişir.</span><span class="sxs-lookup"><span data-stu-id="53674-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="53674-244">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="53674-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="53674-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="53674-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="53674-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="53674-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="53674-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="53674-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="53674-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="53674-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="53674-249">Gerekli olmadığı takdirde proxy arabelleklerinin varsayılan değerlerini artırmaz.</span><span class="sxs-lookup"><span data-stu-id="53674-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="53674-250">Bu değerlerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="53674-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="53674-251">Uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="53674-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="53674-252">AppArmor etkinleştir</span><span class="sxs-lookup"><span data-stu-id="53674-252">Enable AppArmor</span></span>

<span data-ttu-id="53674-253">Linux güvenlik modülleri (LSM), Linux 2,6 ' den beri Linux çekirdeğinin parçası olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="53674-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="53674-254">LSM, güvenlik modüllerinin farklı uygulamalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="53674-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="53674-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) , programı sınırlı bir kaynak kümesiyle sınırlandırarak zorunlu bir Access Control sistemi uygulayan bir LSM 'dir.</span><span class="sxs-lookup"><span data-stu-id="53674-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="53674-256">AppArmor etkinleştirildiğinden ve düzgün şekilde yapılandırıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="53674-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="53674-257">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="53674-257">Configure the firewall</span></span>

<span data-ttu-id="53674-258">Kullanımda olmayan tüm dış bağlantı noktalarını kapatın.</span><span class="sxs-lookup"><span data-stu-id="53674-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="53674-259">Karmaşık olmayan güvenlik duvarı (UW) `iptables` , güvenlik duvarını yapılandırmak için BIR CLI sağlayarak bir ön uç sağlar.</span><span class="sxs-lookup"><span data-stu-id="53674-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="53674-260">Bir güvenlik duvarı, doğru yapılandırılmamışsa tüm sisteme erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="53674-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="53674-261">Kendisine bağlanmak için SSH kullanıyorsanız doğru SSH bağlantı noktasını belirtmemesi Sistem oturumunuzu etkin bir şekilde kilitleyecek.</span><span class="sxs-lookup"><span data-stu-id="53674-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="53674-262">Varsayılan bağlantı noktası 22 ' dir.</span><span class="sxs-lookup"><span data-stu-id="53674-262">The default port is 22.</span></span> <span data-ttu-id="53674-263">Daha fazla bilgi için bkz. [UFW 'ye giriş](https://help.ubuntu.com/community/UFW) ve [el ile](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="53674-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="53674-264">`ufw`Gerekli olan herhangi bir bağlantı noktasında trafiğe izin vermek için bunu yükleyip yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="53674-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="53674-265">Güvenli NGINX</span><span class="sxs-lookup"><span data-stu-id="53674-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="53674-266">NGINX yanıt adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="53674-266">Change the Nginx response name</span></span>

<span data-ttu-id="53674-267">*Src/http/ngx_http_header_filter_module. c* 'yi düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="53674-267">Edit *src/http/ngx_http_header_filter_module.c* :</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="53674-268">Seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="53674-268">Configure options</span></span>

<span data-ttu-id="53674-269">Sunucuyu gerekli olan ek modüllerle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="53674-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="53674-270">Uygulamayı sağlamlaştırmak için [ModSecurity](https://www.modsecurity.org/)gibi bir Web uygulaması güvenlik duvarı kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="53674-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="53674-271">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="53674-271">HTTPS configuration</span></span>

<span data-ttu-id="53674-272">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="53674-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="53674-273">[DotNet Run](/dotnet/core/tools/dotnet-run) komutu, uygulamayı özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandıran, uygulamanın *Özellikler/launchSettings.js* kullanır `applicationUrl` (örneğin, `https://localhost:5001;http://localhost:5000` ).</span><span class="sxs-lookup"><span data-stu-id="53674-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="53674-274">`dotnet run`Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + f5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="53674-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="53674-275">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) ( *önerilir* )</span><span class="sxs-lookup"><span data-stu-id="53674-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) ( *Recommended* )</span></span>
* [<span data-ttu-id="53674-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="53674-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="53674-277">**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="53674-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="53674-278">`443`Güvenilen bir sertifika yetkilisi (CA) tarafından verilen geçerli bir sertifika belirterek, sunucu bağlantı NOKTASıNDA HTTPS trafiğini dinleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="53674-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="53674-279">Aşağıdaki */etc/nginx/nginx.conf* dosyasında gösterilen bazı uygulamalardan yararlanarak güvenliği en iyi şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="53674-279">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="53674-280">Daha güçlü bir şifre seçme ve HTTP üzerinden tüm trafiği HTTPS 'ye yeniden yönlendirme örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="53674-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="53674-281">`HTTP Strict-Transport-Security`(HSTS) üstbilgisi eklemek, istemci tarafından yapılan tüm sonraki ISTEKLERIN https üzerinden yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="53674-281">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="53674-282">Daha sonra HTTPS devre dışı bırakılabiliyorsanız, aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="53674-282">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="53674-283">HSTS üst bilgisini eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="53674-283">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="53674-284">Kısa bir `max-age` değer seçin.</span><span class="sxs-lookup"><span data-stu-id="53674-284">Choose a short `max-age` value.</span></span>

<span data-ttu-id="53674-285">*/Etc/nginx/proxy.conf* yapılandırma dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="53674-285">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="53674-286">*/Etc/nginx/nginx.conf* yapılandırma dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-286">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="53674-287">Örnek, `http` `server` tek bir yapılandırma dosyasında hem hem de bölümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="53674-287">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="53674-288">:::no-loc(Blazor WebAssembly)::: uygulamalar `burst` , bir uygulama tarafından yapılan isteklerin daha fazla sayısına uyum sağlamak için daha büyük bir parametre değeri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="53674-288">:::no-loc(Blazor WebAssembly)::: apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="53674-289">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="53674-289">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="53674-290">Tıklama mercekten NGINX 'i güvenli hale getirme</span><span class="sxs-lookup"><span data-stu-id="53674-290">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="53674-291">*UI redki saldırısı* olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur.</span><span class="sxs-lookup"><span data-stu-id="53674-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack* , is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="53674-292">`X-FRAME-OPTIONS`Sitesini güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="53674-292">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="53674-293">Tıklama saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="53674-293">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="53674-294">*NGINX. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="53674-294">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="53674-295">`add_header X-Frame-Options "SAMEORIGIN";` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="53674-295">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="53674-296">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="53674-296">Save the file.</span></span>
1. <span data-ttu-id="53674-297">NGINX 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="53674-297">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="53674-298">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="53674-298">MIME-type sniffing</span></span>

<span data-ttu-id="53674-299">Bu üst bilgi, tarayıcının, yanıt içerik türünü geçersiz kılmamasını bildiren, büyük bir olasılıkla, MIME tarafından yapılan bir yanıtın bir yanıt olarak bildirimde bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="53674-299">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="53674-300">`nosniff`Seçeneğiyle, sunucu içeriği "metin/html" olarak söyliyorsa, tarayıcı bunu "metin/html" olarak işler.</span><span class="sxs-lookup"><span data-stu-id="53674-300">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="53674-301">*NGINX. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="53674-301">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="53674-302">Satırı ekleyin `add_header X-Content-Type-Options "nosniff";` ve dosyayı kaydedin, sonra NGINX 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="53674-302">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="53674-303">Ek NGINX önerileri</span><span class="sxs-lookup"><span data-stu-id="53674-303">Additional Nginx suggestions</span></span>

<span data-ttu-id="53674-304">Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="53674-304">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53674-305">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="53674-305">Additional resources</span></span>

* [<span data-ttu-id="53674-306">Linux üzerinde .NET Core önkoşulları</span><span class="sxs-lookup"><span data-stu-id="53674-306">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="53674-307">NGINX: Ikili yayınlar: resmi olmayan/Ubuntu Paketleri</span><span class="sxs-lookup"><span data-stu-id="53674-307">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="53674-308">NGıNX: Iletilen üstbilgiyi kullanma</span><span class="sxs-lookup"><span data-stu-id="53674-308">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
