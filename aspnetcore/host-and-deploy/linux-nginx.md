---
title: Host ASP.NET Core Nginx ile Linux üzerinde
author: rick-anderson
description: Http trafiğini Kestrel'de çalışan bir ASP.NET Core web uygulamasına iletmek için Nginx'i Ubuntu 16.04'te ters proxy olarak nasıl kurarak kurabilirsiniz.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: ceb2ad857649dcfa8d04420dcc37792495edc3ff
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224030"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="f0b82-103">Host ASP.NET Core Nginx ile Linux üzerinde</span><span class="sxs-lookup"><span data-stu-id="f0b82-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="f0b82-104">Yazar: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="f0b82-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="f0b82-105">Bu kılavuz, ubuntu 16.04 sunucusunda üretime hazır ASP.NET Core ortamının ayarlanması açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="f0b82-106">Bu yönergeler büyük olasılıkla Ubuntu'nun yeni sürümleriyle çalışır, ancak yönergeler yeni sürümlerle sınanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="f0b82-107">ASP.NET Core tarafından desteklenen diğer Linux dağıtımları hakkında bilgi için [Linux'ta .NET Core için Ön koşullara](/dotnet/core/linux-prerequisites)bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="f0b82-108">Ubuntu 14.04 için Kerkenez işleminin izlenmesi için bir çözüm olarak *denetlenmesi* önerilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="f0b82-109">*sistemli* Ubuntu 14.04'te kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="f0b82-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="f0b82-110">Ubuntu 14.04 yönergeleri için [bu konunun önceki sürümüne](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="f0b82-111">Bu kılavuz:</span><span class="sxs-lookup"><span data-stu-id="f0b82-111">This guide:</span></span>

* <span data-ttu-id="f0b82-112">Varolan bir ASP.NET Core uygulamasını ters proxy sunucusunun arkasına yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="f0b82-113">İstenilenleri Kestrel web sunucusuna iletmek için ters proxy sunucusunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="f0b82-114">Web uygulamasının başlangıç olarak daemon olarak çalıştırMasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="f0b82-115">Web uygulamasını yeniden başlatmaya yardımcı olacak bir işlem yönetimi aracını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0b82-116">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f0b82-116">Prerequisites</span></span>

1. <span data-ttu-id="f0b82-117">Sudo ayrıcalığına sahip standart bir kullanıcı hesabı olan bir Ubuntu 16.04 sunucusuna erişim.</span><span class="sxs-lookup"><span data-stu-id="f0b82-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="f0b82-118">Sunucuya .NET Core çalışma saatini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="f0b82-119">İndir [.NET Çekirdek sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="f0b82-120">En son önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="f0b82-121">Uygulamaları Çalıştır - Runtime altında tabloda en son önizleme olmayan çalışma **süresini indirin.**</span><span class="sxs-lookup"><span data-stu-id="f0b82-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="f0b82-122">Linux **Paketi yöneticisi talimatları** bağlantısını seçin ve Ubuntu sürümünüz için Ubuntu yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="f0b82-123">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="f0b82-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="f0b82-124">Paylaşılan çerçeveyi yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="f0b82-125">Uygulama üzerinde yayımlama ve kopyalama</span><span class="sxs-lookup"><span data-stu-id="f0b82-125">Publish and copy over the app</span></span>

<span data-ttu-id="f0b82-126">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="f0b82-127">Uygulama yerel olarak çalıştırılırsa ve güvenli bağlantılar (HTTPS) yapacak şekilde yapılandırılmamışsa, aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="f0b82-128">Uygulamayı güvenli yerel bağlantıları işleyeceğini zedele.</span><span class="sxs-lookup"><span data-stu-id="f0b82-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="f0b82-129">Daha fazla bilgi için [HTTPS yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="f0b82-130">`https://localhost:5001` *Properties/launchSettings.json* dosyasındaki `applicationUrl` özellikten (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="f0b82-131">Bir uygulamayı sunucuda çalıştırabilen bir dizine (örneğin, *&lt;bin/Release/ target_framework_moniker&gt;/publish)* paketlemek için geliştirme ortamından [dotnet yayımlamayı](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="f0b82-132">Sunucuda .NET Core çalışma süresini korumamak isterseniz, uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayınlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="f0b82-133">kuruluşun iş akışına entegre olan bir aracı kullanarak ASP.NET Core uygulamasını sunucuya kopyalayın (örneğin, SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="f0b82-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="f0b82-134">Var *dizininin* altında web uygulamalarını bulmak yaygındır (örneğin, *var/www/helloapp).*</span><span class="sxs-lookup"><span data-stu-id="f0b82-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="f0b82-135">Üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="f0b82-136">Uygulamayı test edin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-136">Test the app:</span></span>

1. <span data-ttu-id="f0b82-137">Komut satırından uygulamayı çalıştırın: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="f0b82-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="f0b82-138">Bir tarayıcıda, `http://<serveraddress>:<port>` uygulamanın Linux'ta yerel olarak çalıştığını doğrulamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="f0b82-139">Ters proxy sunucusu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f0b82-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="f0b82-140">Ters proxy, dinamik web uygulamaları için ortak bir kurulumdur.</span><span class="sxs-lookup"><span data-stu-id="f0b82-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="f0b82-141">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET Core uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="f0b82-142">Ters proxy sunucusu kullanma</span><span class="sxs-lookup"><span data-stu-id="f0b82-142">Use a reverse proxy server</span></span>

<span data-ttu-id="f0b82-143">Kerkenez, ASP.NET Core'dan dinamik içerik sunan harika bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="f0b82-144">Ancak, web hizmet özellikleri IIS, Apache veya Nginx gibi sunucular kadar zengin özellik değildir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f0b82-145">Ters proxy sunucusu, http sunucusundan statik içerik sunma, istekleri önbelleğe alma, istekleri sıkıştırma ve HTTPS sonlandırma gibi işleri boşaltabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="f0b82-146">Ters proxy sunucusu özel bir makinede bulunabilir veya bir HTTP sunucusunun yanında dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="f0b82-147">Bu kılavuzun amaçları için, Nginx tek bir örnek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="f0b82-148">HTTP sunucusunun yanında aynı sunucuda çalışır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="f0b82-149">Gereksinimlere bağlı olarak, farklı bir kurulum seçilebilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="f0b82-150">İstekler ters proxy ile iletilir olduğundan, [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden [Iletili Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="f0b82-151">Orta `Request.Scheme`yazılım, üstbilgi `X-Forwarded-Proto` kullanarak, URI'leri ve diğer güvenlik ilkelerini yeniden yönlendirmenin doğru çalışmasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="f0b82-152">Kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirmeler ve coğrafi konum gibi şemaya bağlı olan tüm bileşen, İlliyeli Üstbilgi Middleware'i çağırdıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="f0b82-153">Genel bir kural olarak, Iletili Üstbilgi Middleware tanılama ve hata işleme middleware dışında diğer ara önce çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="f0b82-154">Bu sıralama, iletilen üstbilgiler bilgilerine güvenen ara yazılımın işlem için üstbilgi değerlerini tüketmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="f0b82-155"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> Diğer ara yazılımları `Startup.Configure` çağırmadan önce yöntemi en üstteki yönteme çağırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="f0b82-156">Orta yazılımı ve `X-Forwarded-For` üstbilgiileri `X-Forwarded-Proto` iletecek şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="f0b82-157">Ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> yazılımda hayır belirtilmemişse, ileriye dönük `None`varsayılan üstbilgi .</span><span class="sxs-lookup"><span data-stu-id="f0b82-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="f0b82-158">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri dönüş adreslerinde çalışan ekseksenler varsayılan olarak güvenilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="f0b82-159">Kuruluş içindeki diğer güvenilir vekiller veya ağlar, Internet ve web sunucusu arasındaki istekleri <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> işlerse, bunları listeye <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="f0b82-160">Aşağıdaki örnek, IP adresi 10.0.0.100'de güvenilir bir proxy sunucusunu `KnownProxies` `Startup.ConfigureServices`Ilileli Üstbilgi Orta Ware'e ekler:</span><span class="sxs-lookup"><span data-stu-id="f0b82-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="f0b82-161">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f0b82-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="f0b82-162">Nginx'i yükleyin</span><span class="sxs-lookup"><span data-stu-id="f0b82-162">Install Nginx</span></span>

<span data-ttu-id="f0b82-163">Nginx yüklemek için kullanın. `apt-get`</span><span class="sxs-lookup"><span data-stu-id="f0b82-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="f0b82-164">Yükleyici, sistem başlatmada Nginx'i daemon olarak çalıştıran *sistemli* bir init komut dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f0b82-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="f0b82-165">Nginx de Ubuntu için kurulum talimatları [izleyin: Resmi Debian / Ubuntu paketleri](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="f0b82-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="f0b82-166">İsteğe bağlı Nginx modülleri gerekiyorsa, kaynaktan Nginx bina gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="f0b82-167">Nginx ilk kez kurulduğundan, açıkça çalıştırarak başlatın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="f0b82-168">Bir tarayıcının Nginx için varsayılan açılış sayfasını görüntülenin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="f0b82-169">Açılış sayfasına ' `http://<server_IP_address>/index.nginx-debian.html`dan ulaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="f0b82-170">Nginx hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f0b82-170">Configure Nginx</span></span>

<span data-ttu-id="f0b82-171">Nginx'i ASP.NET Core uygulamanıza istekleri iletmek için ters proxy olarak yapılandırmak için, */etc/nginx/sites-available/default adresini değiştirin.*</span><span class="sxs-lookup"><span data-stu-id="f0b82-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="f0b82-172">Bir metin düzenleyicisinde açın ve içeriği aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-172">Open it in a text editor, and replace the contents with the following:</span></span>

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

<span data-ttu-id="f0b82-173">Uygulama SignalR WebSockets'e dayanan bir Blazor Server <xref:host-and-deploy/blazor/server#linux-with-nginx> uygulamasıysa, üstbilginin `Connection` nasıl ayarlanabildiğini öğrenin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="f0b82-174">Eşleşme `server_name` olmadığında, Nginx varsayılan sunucuyu kullanır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="f0b82-175">Varsayılan sunucu tanımlanmamışsa, yapılandırma dosyasındaki ilk sunucu varsayılan sunucudur.</span><span class="sxs-lookup"><span data-stu-id="f0b82-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="f0b82-176">En iyi yöntem olarak, yapılandırma dosyanızda 444'lük bir durum kodu döndüren belirli bir varsayılan sunucu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="f0b82-177">Varsayılan sunucu yapılandırma örneği:</span><span class="sxs-lookup"><span data-stu-id="f0b82-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="f0b82-178">Önceki yapılandırma dosyası ve varsayılan sunucu ile Nginx ana bilgisayar üstbilgi `example.com` veya `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="f0b82-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="f0b82-179">Bu ana bilgisayarlarla eşleşmeyen istekler Kestrel'e iletilmeyecek.</span><span class="sxs-lookup"><span data-stu-id="f0b82-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="f0b82-180">Nginx eşleşen istekleri Kerkenez'e `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f0b82-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="f0b82-181">[Nginx'in daha](https://nginx.org/docs/http/request_processing.html) fazla bilgi isteğini nasıl işlediğini görün.</span><span class="sxs-lookup"><span data-stu-id="f0b82-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="f0b82-182">Kerkenez'in IP/bağlantı noktasını değiştirmek için [Kerkenez: Bitiş Noktası yapılandırmasına](xref:fundamentals/servers/kestrel#endpoint-configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="f0b82-183">Uygun bir [server_name yönergesi](https://nginx.org/docs/http/server_names.html) belirtilmemesi, uygulamanızı güvenlik açıklarına maruz bırakır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="f0b82-184">Alt etki alanı joker karakter `*.example.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="f0b82-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="f0b82-185">Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="f0b82-186">Nginx yapılandırması kurulduktan sonra, yapılandırma dosyalarının sözdizimini doğrulamak için çalıştırın. `sudo nginx -t`</span><span class="sxs-lookup"><span data-stu-id="f0b82-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="f0b82-187">Yapılandırma dosyası testi başarılı olursa, Nginx'i çalıştırarak `sudo nginx -s reload`değişiklikleri almaya zorlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="f0b82-188">Uygulamayı doğrudan sunucuda çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="f0b82-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="f0b82-189">Uygulamanın dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="f0b82-190">Uygulamayı çalıştırın: `dotnet <app_assembly.dll>` `app_assembly.dll` , uygulamanın montaj dosyası adı nerededir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="f0b82-191">Uygulama sunucuda çalışıyorsa ancak Internet üzerinden yanıt veremediyse, sunucunun güvenlik duvarını kontrol edin ve bağlantı noktası 80'in açık olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="f0b82-192">Bir Azure Ubuntu VM kullanıyorsanız, gelen bağlantı noktası 80 trafiğine olanak tanıyan bir Ağ Güvenlik Grubu (NSG) kuralı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="f0b82-193">Giden kural etkinleştirildiğinden, giden trafik otomatik olarak verildiğinden, giden bağlantı noktası 80 kuralını etkinleştirmeye gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f0b82-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="f0b82-194">Uygulamayı test etmek bittiğinde, uygulamayı `Ctrl+C` komut istemiyle kapatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="f0b82-195">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="f0b82-195">Monitor the app</span></span>

<span data-ttu-id="f0b82-196">Sunucu, kestrel'de `http://127.0.0.1:5000` `http://<serveraddress>:80` çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek için kurulumdur.</span><span class="sxs-lookup"><span data-stu-id="f0b82-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="f0b82-197">Ancak, Nginx Kerkenez işlemini yönetmek için kurulmaz.</span><span class="sxs-lookup"><span data-stu-id="f0b82-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="f0b82-198">*sistemli* başlatmak ve altta yatan web uygulamasını izlemek için bir hizmet dosyası oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="f0b82-199">*sistemli,* süreçleri başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="f0b82-200">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f0b82-200">Create the service file</span></span>

<span data-ttu-id="f0b82-201">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f0b82-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="f0b82-202">Uygulama için örnek bir hizmet dosyası aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f0b82-202">The following is an example service file for the app:</span></span>

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

<span data-ttu-id="f0b82-203">Önceki örnekte, hizmeti yöneten kullanıcı `User` seçeneği tarafından belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="f0b82-204">Kullanıcı (`www-data`) var olmalı ve uygulamanın dosyalarının düzgün sahipliğine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="f0b82-205">Uygulamanın ilk kesme sinyalini aldıktan sonra kapanmasını beklemek için süreyi yapılandırmak için kullanın. `TimeoutStopSec`</span><span class="sxs-lookup"><span data-stu-id="f0b82-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="f0b82-206">Bu süre içinde uygulama kapanmazsa, uygulamayı sonlandırmak için SIGKILL verilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="f0b82-207">Değeri birimsiz saniye (örneğin, `150`), zaman aralığı değeri (örneğin, `infinity` `2min 30s`) olarak veya zaman aralığını devre dışı düşürün.</span><span class="sxs-lookup"><span data-stu-id="f0b82-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="f0b82-208">`TimeoutStopSec``DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değere varsayılanlar (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="f0b82-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="f0b82-209">Çoğu dağıtım için varsayılan zaman ası 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="f0b82-210">Linux'un büyük/küçük harf duyarlı bir dosya sistemi vardır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="f0b82-211">ASPNETCORE_ENVIRONMENT "Üretim" olarak ayarlamak, yapılandırma dosyası ayarlarının aranmasıyla *sonuçlanır. Production.json*, *appsettings.production.json*değil .</span><span class="sxs-lookup"><span data-stu-id="f0b82-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="f0b82-212">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerlerin (örneğin, SQL bağlantı dizeleri) kaçılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="f0b82-213">Yapılandırma dosyasında kullanılmak üzere düzgün bir şekilde kaçan bir değer oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="f0b82-214">Kolon`:`( ) ayırıcıları çevre değişken adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f0b82-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="f0b82-215">Bir kolon yerine`__`bir çift alt çizgi ( ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="f0b82-216">[Çevre Değişkenleri yapılandırma sağlayıcısı,](xref:fundamentals/configuration/index#environment-variables-configuration-provider) ortam değişkenleri yapılandırmaya okunduğunda çift alt puanları iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="f0b82-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="f0b82-217">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanım `ConnectionStrings__DefaultConnection`dosyasına şu şekilde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="f0b82-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="f0b82-218">Dosyayı kaydedin ve hizmeti etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="f0b82-219">Hizmeti başlatın ve çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-219">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="f0b82-220">Ters proxy yapılandırılan ve Kestrel sistemli aracılığıyla yönetilen ile, web uygulaması tamamen yapılandırılmışve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="f0b82-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="f0b82-221">Ayrıca, engellenen herhangi bir güvenlik duvarı hariç, uzak bir makineden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="f0b82-222">Yanıt üstaylarını inceleyen `Server` üstbilgi, Kestrel tarafından sunulan ASP.NET Core uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="f0b82-223">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="f0b82-223">View logs</span></span>

<span data-ttu-id="f0b82-224">Kestrel'i kullanan web uygulaması `systemd`kullanılarak yönetildiği için, tüm olaylar ve işlemler merkezi bir günlükte günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="f0b82-225">Ancak, bu günlük tarafından `systemd`yönetilen tüm hizmetler ve işlemler için tüm girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="f0b82-226">`kestrel-helloapp.service`-belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="f0b82-227">Daha fazla filtreleme için, `--since today` `--until 1 hour ago` bu gibi zaman seçenekleri veya bunların bir kombinasyonu döndürülen giriş miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="f0b82-228">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="f0b82-228">Data protection</span></span>

<span data-ttu-id="f0b82-229">[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulama ara yazılımları (örneğin, çerez ara yazılımları) ve site ler arası istek sahteciliği (CSRF) korumaları da dahil olmak üzere birkaç ASP.NET Core ara [yazılımtarafından](xref:fundamentals/middleware/index)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="f0b82-230">Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme [anahtar deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="f0b82-231">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="f0b82-232">Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:</span><span class="sxs-lookup"><span data-stu-id="f0b82-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="f0b82-233">Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="f0b82-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="f0b82-234">Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="f0b82-235">Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="f0b82-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="f0b82-236">Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="f0b82-237">Anahtarlık devam etmek ve şifrelemek için veri korumayapılandırmak için bkz:</span><span class="sxs-lookup"><span data-stu-id="f0b82-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="f0b82-238">Uzun istek üstbilgi alanları</span><span class="sxs-lookup"><span data-stu-id="f0b82-238">Long request header fields</span></span>

<span data-ttu-id="f0b82-239">Proxy sunucusu varsayılan ayarları genellikle platforma bağlı olarak istek üstbilgisi alanlarını 4 K veya 8 K ile sınırlandırırlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="f0b82-240">Bir uygulama varsayılan dan daha uzun alanlar gerektirebilir (örneğin, [Azure Etkin Dizinkullanan](https://azure.microsoft.com/services/active-directory/)uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="f0b82-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="f0b82-241">Daha uzun alanlar gerekiyorsa, proxy sunucusunun varsayılan ayarları ayarlama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="f0b82-242">Uygulanacak değerler senaryoya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="f0b82-243">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="f0b82-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="f0b82-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="f0b82-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="f0b82-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="f0b82-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="f0b82-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="f0b82-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="f0b82-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="f0b82-248">Gerekli olmadıkça proxy arabelleklerinin varsayılan değerlerini artırmayın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="f0b82-249">Bu değerlerin artırılması, arabellek taşması (taşması) ve kötü amaçlı kullanıcıların Hizmet Reddi (DoS) saldırılarına riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="f0b82-250">Uygulamayı güvenli hale</span><span class="sxs-lookup"><span data-stu-id="f0b82-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="f0b82-251">AppArmor etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="f0b82-251">Enable AppArmor</span></span>

<span data-ttu-id="f0b82-252">Linux Güvenlik Modülleri (LSM), Linux 2.6'dan beri Linux çekirdeğinin bir parçası olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="f0b82-253">LSM, güvenlik modüllerinin farklı uygulamalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="f0b82-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="f0b82-254">[AppArmor,](https://wiki.ubuntu.com/AppArmor) programın sınırlı bir kaynak kümesiyle sınırlanmasına olanak tanıyan bir Zorunlu Erişim Kontrol sistemi uygulayan bir LSM'dir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="f0b82-255">AppArmor'un etkin olduğundan ve doğru şekilde yapılandırıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="f0b82-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="f0b82-256">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f0b82-256">Configure the firewall</span></span>

<span data-ttu-id="f0b82-257">Kullanılmayan tüm harici bağlantı noktalarını kapatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="f0b82-258">Karmaşık olmayan güvenlik duvarı (ufw), `iptables` güvenlik duvarını yapılandırmak için bir CLI sağlayarak ön uç sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="f0b82-259">Güvenlik duvarı, doğru şekilde yapılandırılmazsa tüm sisteme erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="f0b82-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="f0b82-260">Doğru SSH bağlantı noktasının belirtilmemesi, bağlantı kurmak için SSH kullanıyorsanız sizi etkin bir şekilde sistemin dışına kilitler.</span><span class="sxs-lookup"><span data-stu-id="f0b82-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="f0b82-261">Varsayılan bağlantı noktası 22'dir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-261">The default port is 22.</span></span> <span data-ttu-id="f0b82-262">Daha fazla bilgi [için, ufw](https://help.ubuntu.com/community/UFW) ve [kılavuzuna](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)giriş bakın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="f0b82-263">Gerekli `ufw` tüm bağlantı noktalarında trafiğe izin verecek şekilde yükleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="f0b82-264">Güvenli Nginx</span><span class="sxs-lookup"><span data-stu-id="f0b82-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="f0b82-265">Nginx yanıt adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="f0b82-265">Change the Nginx response name</span></span>

<span data-ttu-id="f0b82-266">*Src/http/ngx_http_header_filter_module.c'yi*edit:</span><span class="sxs-lookup"><span data-stu-id="f0b82-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="f0b82-267">Seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f0b82-267">Configure options</span></span>

<span data-ttu-id="f0b82-268">Sunucuyu gerekli ek modüllerle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="f0b82-269">Uygulamayı sertleştirmek için [ModSecurity](https://www.modsecurity.org/)gibi bir web uygulaması güvenlik duvarı kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="f0b82-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="f0b82-270">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f0b82-270">HTTPS configuration</span></span>

<span data-ttu-id="f0b82-271">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırın**</span><span class="sxs-lookup"><span data-stu-id="f0b82-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="f0b82-272">[Dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutu, uygulamanın özellik tarafından sağlanan URL'leri dinleyecek şekilde yapılandıran *Özellikler/launchSettings.json* dosyasını `applicationUrl` kullanır (örneğin, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="f0b82-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="f0b82-273">Aşağıdaki yaklaşımlardan birini kullanarak uygulamayı `dotnet run` komut veya geliştirme ortamı (Visual Studio Code'ta F5 veya Ctrl+F5) için geliştirme aşamasında bir sertifika kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f0b82-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="f0b82-274">[Yapılandırmadan varsayılan sertifikayı değiştirme](xref:fundamentals/servers/kestrel#configuration) (*Önerilen*)</span><span class="sxs-lookup"><span data-stu-id="f0b82-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="f0b82-275">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="f0b82-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="f0b82-276">**Güvenli (HTTPS) istemci bağlantıları için ters proxy'yi yapılandırın**</span><span class="sxs-lookup"><span data-stu-id="f0b82-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="f0b82-277">Güvenilir bir Sertifika Yetkilisi (CA) `443` tarafından verilen geçerli bir sertifika belirterek sunucuyu bağlantı noktasındaki HTTPS trafiğini dinleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="f0b82-278">Aşağıdaki */etc/nginx/nginx.conf* dosyasında gösterilen bazı uygulamaları uygulayarak güvenliği güçlendirin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="f0b82-279">Örnekler arasında daha güçlü bir şifre seçmek ve HTTP üzerindeki tüm trafiği HTTPS'ye yönlendirmek sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="f0b82-280">`HTTP Strict-Transport-Security` (HSTS) üstbilgi eklemek, istemci tarafından yapılan sonraki tüm isteklerin HTTPS üzerinde olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f0b82-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="f0b82-281">Gelecekte HTTPS devre dışı bırakılırsa HSTS üstbilgisini eklemeyin veya uygun `max-age` bir üstbilgi seçmeyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="f0b82-282">*/etc/nginx/proxy.conf* yapılandırma dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="f0b82-283">*/etc/nginx/nginx.conf* yapılandırma dosyasını düzenleme.</span><span class="sxs-lookup"><span data-stu-id="f0b82-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="f0b82-284">Örnek, tek `http` `server` bir yapılandırma dosyasında hem hem de bölümleri içerir.</span><span class="sxs-lookup"><span data-stu-id="f0b82-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="f0b82-285">Clickjacking güvenli Nginx</span><span class="sxs-lookup"><span data-stu-id="f0b82-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="f0b82-286">*UI telafi saldırısı*olarak da bilinen [Clickjacking,](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)bir web sitesi ziyaretçisi şu anda ziyaret ettiklerinden farklı bir sayfadaki bir bağlantıyı veya düğmeyi tıklatmak için kandırıldığı kötü amaçlı bir saldırıdır.</span><span class="sxs-lookup"><span data-stu-id="f0b82-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="f0b82-287">Siteyi `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="f0b82-288">Tıklama kaçırma saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="f0b82-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="f0b82-289">*Nginx.conf* dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="f0b82-290">`add_header X-Frame-Options "SAMEORIGIN";` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="f0b82-291">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f0b82-291">Save the file.</span></span>
1. <span data-ttu-id="f0b82-292">Nginx'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="f0b82-293">MIME tipi koklama</span><span class="sxs-lookup"><span data-stu-id="f0b82-293">MIME-type sniffing</span></span>

<span data-ttu-id="f0b82-294">Üstbilgi, üstbilgi tarayıcıya yanıt içeriği türünü geçersiz kılmamasını söylerken, bu üstbilgi çoğu tarayıcının bildirilen içerik türünden uzakta bir yanıtı mime koklamasını önler.</span><span class="sxs-lookup"><span data-stu-id="f0b82-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="f0b82-295">`nosniff` Sunucu, içeriğin "text/html" olduğunu söylüyorsa, tarayıcı içeriği "text/html" olarak işler.</span><span class="sxs-lookup"><span data-stu-id="f0b82-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="f0b82-296">*Nginx.conf* dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="f0b82-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="f0b82-297">Satırı `add_header X-Content-Type-Options "nosniff";` ekleyin ve dosyayı kaydedin, sonra Nginx'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="f0b82-298">Ek Nginx önerileri</span><span class="sxs-lookup"><span data-stu-id="f0b82-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="f0b82-299">Sunucuda paylaşılan çerçeveyi yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f0b82-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0b82-300">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f0b82-300">Additional resources</span></span>

* [<span data-ttu-id="f0b82-301">Linux'ta .NET Core için ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f0b82-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="f0b82-302">Nginx: İkili Bültenleri: Resmi Debian / Ubuntu paketleri</span><span class="sxs-lookup"><span data-stu-id="f0b82-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="f0b82-303">NGINX: İlerleyen üstbilginin kullanılması</span><span class="sxs-lookup"><span data-stu-id="f0b82-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
