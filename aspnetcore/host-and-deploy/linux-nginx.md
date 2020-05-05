---
title: NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core
author: rick-anderson
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına iletmek için Ubuntu 16,04 üzerinde ters proxy olarak NGINX 'i ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: af2bea1b3a149ef8d80970031e939dc083d94a03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775901"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="d595e-103">NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d595e-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="d595e-104">, [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="d595e-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="d595e-105">Bu kılavuzda Ubuntu 16,04 sunucusunda üretime hazır ASP.NET Core ortamı ayarlama açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d595e-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="d595e-106">Bu yönergeler büyük olasılıkla Ubuntu 'ın daha yeni sürümleriyle çalışır, ancak yönergeler daha yeni sürümlerle sınanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="d595e-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="d595e-107">ASP.NET Core tarafından desteklenen diğer Linux dağıtımları hakkında daha fazla bilgi için bkz. [Linux üzerinde .NET Core önkoşulları](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="d595e-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="d595e-108">Ubuntu 14,04 için, Kestrel işlemini izlemeye yönelik bir çözüm olarak *supervisof* önerilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="d595e-109">*systemd* , Ubuntu 14,04 ' de kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d595e-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="d595e-110">Ubuntu 14,04 yönergeleri için [Bu konunun önceki sürümüne](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="d595e-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="d595e-111">Bu kılavuz:</span><span class="sxs-lookup"><span data-stu-id="d595e-111">This guide:</span></span>

* <span data-ttu-id="d595e-112">Mevcut bir ASP.NET Core uygulamasını bir ters proxy sunucusunun arkasına koyar.</span><span class="sxs-lookup"><span data-stu-id="d595e-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="d595e-113">İstekleri Kestrel Web sunucusuna iletmek için ters proxy sunucusunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d595e-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="d595e-114">Web uygulamasının, bir arka plan programı olarak başlangıcında çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d595e-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="d595e-115">Web uygulamasını yeniden başlatmanıza yardımcı olması için bir işlem yönetim aracı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="d595e-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d595e-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="d595e-116">Prerequisites</span></span>

1. <span data-ttu-id="d595e-117">Sudo ayrıcalığına sahip standart bir kullanıcı hesabı ile Ubuntu 16,04 sunucusuna erişim.</span><span class="sxs-lookup"><span data-stu-id="d595e-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="d595e-118">.NET Core çalışma zamanını sunucuya yükler.</span><span class="sxs-lookup"><span data-stu-id="d595e-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="d595e-119">[.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="d595e-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="d595e-120">En son Önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="d595e-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="d595e-121">**Uygulama çalıştırma-çalışma zamanı**altındaki tabloda en son önizleme dışı çalışma zamanını indirin.</span><span class="sxs-lookup"><span data-stu-id="d595e-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="d595e-122">Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve Ubuntu sürümünüz Için Ubuntu yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="d595e-123">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="d595e-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="d595e-124">Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="d595e-125">Uygulama üzerinde Yayımla ve Kopyala</span><span class="sxs-lookup"><span data-stu-id="d595e-125">Publish and copy over the app</span></span>

<span data-ttu-id="d595e-126">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="d595e-127">Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d595e-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="d595e-128">Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="d595e-129">Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d595e-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="d595e-130">`https://localhost:5001` *Properties/launchsettings. JSON* dosyasındaki `applicationUrl` özelliğinden (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="d595e-131">Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/&lt;target_framework_moniker&gt;/Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d595e-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="d595e-132">Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="d595e-133">ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="d595e-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="d595e-134">*Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="d595e-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="d595e-135">Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="d595e-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="d595e-136">Uygulamayı test etme:</span><span class="sxs-lookup"><span data-stu-id="d595e-136">Test the app:</span></span>

1. <span data-ttu-id="d595e-137">Komut satırından uygulamayı çalıştırın: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="d595e-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="d595e-138">Bir tarayıcıda, uygulamanın Linux üzerinde `http://<serveraddress>:<port>` yerel olarak çalıştığını doğrulamak için bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="d595e-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="d595e-139">Ters proxy sunucu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d595e-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="d595e-140">Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir.</span><span class="sxs-lookup"><span data-stu-id="d595e-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="d595e-141">Ters proxy, HTTP isteğini sonlandırır ve ASP.NET Core uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="d595e-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="d595e-142">Ters proxy sunucusu kullanma</span><span class="sxs-lookup"><span data-stu-id="d595e-142">Use a reverse proxy server</span></span>

<span data-ttu-id="d595e-143">Kestrel, ASP.NET Core ' den dinamik içerik sunmak için harika.</span><span class="sxs-lookup"><span data-stu-id="d595e-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="d595e-144">Ancak, Web 'e sunma özellikleri IIS, Apache veya NGINX gibi sunucu olarak zengin özellik değildir.</span><span class="sxs-lookup"><span data-stu-id="d595e-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="d595e-145">Ters bir ara sunucu, statik içerik sunma, istekleri önbelleğe alma, istekleri sıkıştırma ve HTTP sunucusundan HTTPS sonlandırması gibi işleri devreedebilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="d595e-146">Ters ara sunucu, ayrılmış bir makinede bulunabilir veya bir HTTP sunucusu ile birlikte dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="d595e-147">Bu kılavuzun amaçları doğrultusunda, tek bir NGINX örneği kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d595e-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="d595e-148">Bu, HTTP sunucusu ile birlikte aynı sunucuda çalışır.</span><span class="sxs-lookup"><span data-stu-id="d595e-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="d595e-149">Gereksinimlere bağlı olarak, farklı bir kurulum seçilebilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="d595e-150">İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="d595e-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="d595e-151">Ara yazılım, `X-Forwarded-Proto` üstbilgiyi `Request.Scheme`kullanarak, yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="d595e-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="d595e-152">Bir şemaya bağlı kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirme ve coğrafi konum gibi herhangi bir bileşen, Iletilen üstbilgiler ara yazılımı çağrıldıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d595e-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="d595e-153">Genel bir kural olarak, Iletilen üstbilgiler ara yazılımı, tanılama ve hata işleme ara yazılımı dışında diğer ara yazılım ile önce çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d595e-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="d595e-154">Bu sıralama, iletilen üst bilgi bilgilerine bağlı olan ara yazılımın işleme için üst bilgi değerlerini kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d595e-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="d595e-155">Diğer ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> yazılım çağrılmadan `Startup.Configure` önce yönteminin en üstünde yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="d595e-156">Ara yazılımı, `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini iletecek şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="d595e-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="d595e-157">Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None`.</span><span class="sxs-lookup"><span data-stu-id="d595e-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="d595e-158">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir.</span><span class="sxs-lookup"><span data-stu-id="d595e-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="d595e-159">Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> ile <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="d595e-160">Aşağıdaki örnek, içindeki `KnownProxies` `Startup.ConfigureServices`iletilen ÜSTBILGILER ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler:</span><span class="sxs-lookup"><span data-stu-id="d595e-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="d595e-161">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d595e-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="d595e-162">NGINX 'i yükler</span><span class="sxs-lookup"><span data-stu-id="d595e-162">Install Nginx</span></span>

<span data-ttu-id="d595e-163">NGINX yüklemek için kullanın `apt-get` .</span><span class="sxs-lookup"><span data-stu-id="d595e-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="d595e-164">Yükleyici, sistem başlangıcında arka plan programı olarak NGINX çalıştıran bir *systemd* init betiği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d595e-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="d595e-165">[NGINX: resmi deni/Ubuntu paketlerinde](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)Ubuntu yükleme yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="d595e-166">İsteğe bağlı NGINX modülleri gerekliyse, kaynaktan NGINX oluşturulması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="d595e-167">NGINX ilk kez yüklendiğinden bu yana şunu çalıştırarak açık olarak başlatın:</span><span class="sxs-lookup"><span data-stu-id="d595e-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="d595e-168">Bir tarayıcının NGINX için varsayılan giriş sayfasını görüntülediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d595e-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="d595e-169">Giriş sayfasına adresinden `http://<server_IP_address>/index.nginx-debian.html`ulaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="d595e-170">Nginx hizmetini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d595e-170">Configure Nginx</span></span>

<span data-ttu-id="d595e-171">İstekleri ASP.NET Core uygulamanıza iletmek için NGINX 'i ters proxy olarak yapılandırmak için */etc/nginx/sites-available/default*değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d595e-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="d595e-172">Bu dosyayı bir metin düzenleyicisinde açın ve içeriği şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d595e-172">Open it in a text editor, and replace the contents with the following:</span></span>

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

<span data-ttu-id="d595e-173">Blazor Uygulama, SignalR WebSockets kullanan bir sunucu uygulaması ise, <xref:host-and-deploy/blazor/server#linux-with-nginx> `Connection` üstbilgiyi ayarlama hakkında daha fazla bilgi için bkz..</span><span class="sxs-lookup"><span data-stu-id="d595e-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="d595e-174">Hiçbir `server_name` eşleşme olmadığında NGINX varsayılan sunucuyu kullanır.</span><span class="sxs-lookup"><span data-stu-id="d595e-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="d595e-175">Varsayılan sunucu tanımlanmazsa, yapılandırma dosyasındaki ilk sunucu varsayılan sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d595e-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="d595e-176">En iyi uygulama olarak, yapılandırma dosyanızda 444 durum kodunu döndüren belirli bir varsayılan sunucu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="d595e-177">Varsayılan bir sunucu yapılandırma örneği:</span><span class="sxs-lookup"><span data-stu-id="d595e-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="d595e-178">Önceki yapılandırma dosyası ve varsayılan sunucu ile NGINX, bağlantı noktası 80 üzerinde ana bilgisayar üst bilgisi `example.com` veya `*.example.com`olan genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d595e-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="d595e-179">Bu konaklarla eşleşmeyen istekler Kestrel 'e iletilemiyor.</span><span class="sxs-lookup"><span data-stu-id="d595e-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="d595e-180">NGINX eşleşen istekleri şurada `http://localhost:5000`Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="d595e-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="d595e-181">Daha fazla bilgi için [NGINX 'in Isteği nasıl işliyorsa öğrenin](https://nginx.org/docs/http/request_processing.html) .</span><span class="sxs-lookup"><span data-stu-id="d595e-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="d595e-182">Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="d595e-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="d595e-183">Uygun bir [SERVER_NAME yönergesi](https://nginx.org/docs/http/server_names.html) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="d595e-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="d595e-184">Alt etki alanı joker karakteri bağlama ( `*.example.com`Örneğin,), tüm üst etki alanını (Bu güvenlik açığı olan aksine `*.com`) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz.</span><span class="sxs-lookup"><span data-stu-id="d595e-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d595e-185">Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="d595e-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="d595e-186">NGINX yapılandırması kurulduktan sonra, yapılandırma dosyalarının söz `sudo nginx -t` dizimini doğrulamak için ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="d595e-187">Yapılandırma dosyası testi başarılıysa, NGINX ' i çalıştırarak `sudo nginx -s reload`değişiklikleri çekmeye zorlayın.</span><span class="sxs-lookup"><span data-stu-id="d595e-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="d595e-188">Uygulamayı sunucuda doğrudan çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="d595e-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="d595e-189">Uygulamanın dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="d595e-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="d595e-190">Uygulamayı çalıştırın: `dotnet <app_assembly.dll>`, burada `app_assembly.dll` uygulamanın derleme dosyası adıdır.</span><span class="sxs-lookup"><span data-stu-id="d595e-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="d595e-191">Uygulama sunucuda çalışır, ancak Internet üzerinden yanıt vermezse, sunucunun güvenlik duvarını denetleyin ve 80 bağlantı noktasının açık olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d595e-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="d595e-192">Azure Ubuntu VM kullanıyorsanız, gelen bağlantı noktası 80 trafiğine izin veren bir ağ güvenlik grubu (NSG) kuralı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="d595e-193">Giden trafik, gelen kuralı etkinleştirildiğinde otomatik olarak verildiği için, giden bağlantı noktası 80 kuralını etkinleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d595e-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="d595e-194">Uygulamayı test etmeyi tamamladıktan sonra komut isteminde uygulamayı ile `Ctrl+C` kapatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="d595e-195">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="d595e-195">Monitor the app</span></span>

<span data-ttu-id="d595e-196">Sunucu, `http://<serveraddress>:80` üzerinde yapılan Istekleri üzerinde Kestrel üzerinde çalışan ASP.NET Core uygulamasına iletmek üzere ayarlanır `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="d595e-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="d595e-197">Ancak, NGINX Kestrel işlemini yönetmek için ayarlanmadı.</span><span class="sxs-lookup"><span data-stu-id="d595e-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="d595e-198">*systemd* , temel Web uygulamasını başlatmak ve izlemek üzere bir hizmet dosyası oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="d595e-199">*systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="d595e-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="d595e-200">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="d595e-200">Create the service file</span></span>

<span data-ttu-id="d595e-201">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d595e-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="d595e-202">Aşağıda, uygulama için örnek bir hizmet dosyası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d595e-202">The following is an example service file for the app:</span></span>

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

<span data-ttu-id="d595e-203">Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="d595e-204">Kullanıcı (`www-data`) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.</span><span class="sxs-lookup"><span data-stu-id="d595e-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="d595e-205">Uygulamanın `TimeoutStopSec` ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d595e-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="d595e-206">Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d595e-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="d595e-207">Değeri unitless saniyeler (örneğin, `150`), bir zaman aralığı değeri (örneğin, `2min 30s`) veya `infinity` zaman aşımını devre dışı bırakmak için girin.</span><span class="sxs-lookup"><span data-stu-id="d595e-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="d595e-208">`TimeoutStopSec`Varsayılan olarak, yönetici yapılandırma `DefaultTimeoutStopSec` dosyasındaki değerini alır (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*).</span><span class="sxs-lookup"><span data-stu-id="d595e-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="d595e-209">Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="d595e-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="d595e-210">Linux, büyük/küçük harfe duyarlı bir dosya sistemine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d595e-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="d595e-211">ASPNETCORE_ENVIRONMENT "üretim" olarak ayarlamak, yapılandırma dosyası appSettings 'i aramasına neden olur *. Ürün. JSON*, *appSettings. Production. JSON*değil.</span><span class="sxs-lookup"><span data-stu-id="d595e-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="d595e-212">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d595e-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="d595e-213">Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="d595e-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="d595e-214">İki nokta`:`() ayırıcılar ortam değişkeni adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d595e-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="d595e-215">İki nokta üst üste yerine`__`çift alt çizgi () kullanın.</span><span class="sxs-lookup"><span data-stu-id="d595e-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="d595e-216">Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="d595e-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="d595e-217">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde `ConnectionStrings__DefaultConnection`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d595e-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="d595e-218">Dosyayı kaydedin ve hizmeti etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d595e-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="d595e-219">Hizmeti başlatın ve çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="d595e-219">Start the service and verify that it's running.</span></span>

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

<span data-ttu-id="d595e-220">Ters proxy yapılandırılmış ve systemd üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden `http://localhost`yerel makinedeki bir tarayıcıdan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="d595e-221">Ayrıca, uzak bir makineden de erişilebilir, engelleyici olabilecek tüm güvenlik duvarını açabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="d595e-222">Yanıt üst bilgilerini inceleyerek `Server` üst bilgi, Kestrel tarafından sunulan ASP.NET Core uygulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d595e-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="d595e-223">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="d595e-223">View logs</span></span>

<span data-ttu-id="d595e-224">Kestrel kullanan Web uygulaması kullanılarak `systemd`yönetildiğinden, tüm olaylar ve süreçler merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="d595e-225">Ancak bu günlük, tarafından `systemd`yönetilen tüm hizmetler ve süreçler için tüm girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d595e-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="d595e-226">Belirli öğeleri görüntülemek `kestrel-helloapp.service`için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="d595e-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="d595e-227">Daha fazla filtreleme için `--since today`, `--until 1 hour ago` gibi zaman seçenekleri veya bunların bir birleşimi döndürülen girdi miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="d595e-228">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="d595e-228">Data protection</span></span>

<span data-ttu-id="d595e-229">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı (örneğin, tanımlama bilgisi ara yazılımı) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d595e-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="d595e-230">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d595e-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d595e-231">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="d595e-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d595e-232">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="d595e-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d595e-233">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="d595e-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="d595e-234">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d595e-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="d595e-235">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="d595e-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d595e-236">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata)içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d595e-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d595e-237">Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:</span><span class="sxs-lookup"><span data-stu-id="d595e-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="d595e-238">Uzun istek üst bilgisi alanları</span><span class="sxs-lookup"><span data-stu-id="d595e-238">Long request header fields</span></span>

<span data-ttu-id="d595e-239">Proxy sunucusu varsayılan ayarları, platforma bağlı olarak genellikle istek üst bilgisi alanlarını 4 K veya 8 K ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="d595e-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="d595e-240">Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="d595e-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="d595e-241">Daha uzun alanlar gerekliyse, proxy sunucusunun varsayılan ayarları ayarlama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d595e-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="d595e-242">Uygulanacak değerler senaryoya göre değişir.</span><span class="sxs-lookup"><span data-stu-id="d595e-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="d595e-243">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d595e-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="d595e-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="d595e-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="d595e-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="d595e-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="d595e-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="d595e-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="d595e-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="d595e-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="d595e-248">Gerekli olmadığı takdirde proxy arabelleklerinin varsayılan değerlerini artırmaz.</span><span class="sxs-lookup"><span data-stu-id="d595e-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="d595e-249">Bu değerlerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="d595e-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="d595e-250">Uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="d595e-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="d595e-251">AppArmor etkinleştir</span><span class="sxs-lookup"><span data-stu-id="d595e-251">Enable AppArmor</span></span>

<span data-ttu-id="d595e-252">Linux güvenlik modülleri (LSM), Linux 2,6 ' den beri Linux çekirdeğinin parçası olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="d595e-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="d595e-253">LSM, güvenlik modüllerinin farklı uygulamalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="d595e-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="d595e-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) , programı sınırlı bir kaynak kümesiyle sınırlandırarak zorunlu bir Access Control sistemi uygulayan bir LSM 'dir.</span><span class="sxs-lookup"><span data-stu-id="d595e-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="d595e-255">AppArmor etkinleştirildiğinden ve düzgün şekilde yapılandırıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="d595e-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="d595e-256">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d595e-256">Configure the firewall</span></span>

<span data-ttu-id="d595e-257">Kullanımda olmayan tüm dış bağlantı noktalarını kapatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="d595e-258">Karmaşık olmayan güvenlik duvarı (UW), güvenlik duvarını yapılandırmak `iptables` IÇIN bir CLI sağlayarak bir ön uç sağlar.</span><span class="sxs-lookup"><span data-stu-id="d595e-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="d595e-259">Bir güvenlik duvarı, doğru yapılandırılmamışsa tüm sisteme erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="d595e-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="d595e-260">Kendisine bağlanmak için SSH kullanıyorsanız doğru SSH bağlantı noktasını belirtmemesi Sistem oturumunuzu etkin bir şekilde kilitleyecek.</span><span class="sxs-lookup"><span data-stu-id="d595e-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="d595e-261">Varsayılan bağlantı noktası 22 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d595e-261">The default port is 22.</span></span> <span data-ttu-id="d595e-262">Daha fazla bilgi için bkz. [UFW 'ye giriş](https://help.ubuntu.com/community/UFW) ve [el ile](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="d595e-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="d595e-263">Gerekli `ufw` olan herhangi bir bağlantı noktasında trafiğe izin vermek için bunu yükleyip yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="d595e-264">Güvenli NGINX</span><span class="sxs-lookup"><span data-stu-id="d595e-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="d595e-265">NGINX yanıt adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="d595e-265">Change the Nginx response name</span></span>

<span data-ttu-id="d595e-266">*Src/http/ngx_http_header_filter_module. c*'yi düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="d595e-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="d595e-267">Seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d595e-267">Configure options</span></span>

<span data-ttu-id="d595e-268">Sunucuyu gerekli olan ek modüllerle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="d595e-269">Uygulamayı sağlamlaştırmak için [ModSecurity](https://www.modsecurity.org/)gibi bir Web uygulaması güvenlik duvarı kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="d595e-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="d595e-270">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d595e-270">HTTPS configuration</span></span>

<span data-ttu-id="d595e-271">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="d595e-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="d595e-272">[DotNet Run](/dotnet/core/tools/dotnet-run) komutu uygulamanın *Özellikler/launchsettings. JSON* dosyasını kullanır, bu da uygulamayı `applicationUrl` özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandırır (örneğin, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="d595e-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="d595e-273">Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı `dotnet run` komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + F5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="d595e-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="d595e-274">[Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="d595e-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="d595e-275">KestrelServerOptions. ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="d595e-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="d595e-276">**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="d595e-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="d595e-277">Güvenilen bir sertifika yetkilisi (CA) tarafından verilen geçerli `443` bir sertifika belirterek, sunucu bağlantı noktasında HTTPS trafiğini dinleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d595e-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="d595e-278">Aşağıdaki */etc/nginx/nginx.conf* dosyasında gösterilen bazı uygulamalardan yararlanarak güvenliği en iyi şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="d595e-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="d595e-279">Daha güçlü bir şifre seçme ve HTTP üzerinden tüm trafiği HTTPS 'ye yeniden yönlendirme örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d595e-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="d595e-280">`HTTP Strict-Transport-Security` (HSTS) üstbilgisi eklemek, istemci tarafından yapılan tüm sonrakı isteklerin https üzerinden yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d595e-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="d595e-281">Daha sonra HTTPS 'nin devre dışı bırakılacağı için HSTS üst bilgisini eklemeyin veya uygun `max-age` bir tercih seçmeyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="d595e-282">*/Etc/nginx/proxy.conf* yapılandırma dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d595e-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="d595e-283">*/Etc/nginx/nginx.conf* yapılandırma dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="d595e-284">Örnek, tek bir `http` yapılandırma `server` dosyasında hem hem de bölümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d595e-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="d595e-285">Tıklama mercekten NGINX 'i güvenli hale getirme</span><span class="sxs-lookup"><span data-stu-id="d595e-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="d595e-286">*UI redki saldırısı*olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur.</span><span class="sxs-lookup"><span data-stu-id="d595e-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="d595e-287">Sitesini `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d595e-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="d595e-288">Tıklama saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="d595e-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="d595e-289">*NGINX. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="d595e-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="d595e-290">`add_header X-Frame-Options "SAMEORIGIN";` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d595e-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="d595e-291">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d595e-291">Save the file.</span></span>
1. <span data-ttu-id="d595e-292">NGINX 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="d595e-293">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="d595e-293">MIME-type sniffing</span></span>

<span data-ttu-id="d595e-294">Bu üst bilgi, tarayıcının, yanıt içerik türünü geçersiz kılmamasını bildiren, büyük bir olasılıkla, MIME tarafından yapılan bir yanıtın bir yanıt olarak bildirimde bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="d595e-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="d595e-295">`nosniff` Seçeneğiyle, sunucu içeriği "metin/html" olarak söyliyorsa, tarayıcı bunu "metin/html" olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d595e-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="d595e-296">*NGINX. conf* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="d595e-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="d595e-297">Satırı `add_header X-Content-Type-Options "nosniff";` ekleyin ve dosyayı kaydedin, sonra NGINX 'i yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="d595e-298">Ek NGINX önerileri</span><span class="sxs-lookup"><span data-stu-id="d595e-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="d595e-299">Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d595e-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d595e-300">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d595e-300">Additional resources</span></span>

* [<span data-ttu-id="d595e-301">Linux üzerinde .NET Core önkoşulları</span><span class="sxs-lookup"><span data-stu-id="d595e-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="d595e-302">NGINX: Ikili yayınlar: resmi olmayan/Ubuntu Paketleri</span><span class="sxs-lookup"><span data-stu-id="d595e-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d595e-303">NGıNX: Iletilen üstbilgiyi kullanma</span><span class="sxs-lookup"><span data-stu-id="d595e-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
