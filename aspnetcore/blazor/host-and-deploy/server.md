---
title: ASP.NET Core sunucusu barındırma ve dağıtma Blazor
author: guardrex
description: ASP.NET Core kullanarak sunucu uygulamasını nasıl barındırılacağını ve dağıtacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: c02d005aa5e37bc359e1c104a19c387f1c16fa34
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243557"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="c14e0-103">Sunucu barındırma ve dağıtma Blazor</span><span class="sxs-lookup"><span data-stu-id="c14e0-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="c14e0-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="c14e0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c14e0-105">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="c14e0-105">Host configuration values</span></span>

<span data-ttu-id="c14e0-106">[ Blazor Sunucu uygulamaları](xref:blazor/hosting-models#blazor-server) [genel ana bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="c14e0-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="c14e0-107">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="c14e0-107">Deployment</span></span>

<span data-ttu-id="c14e0-108">[ Blazor Sunucu barındırma modelinin](xref:blazor/hosting-models#blazor-server)kullanımı, Blazor sunucuda bir ASP.NET Core uygulamasının içinden yürütülür.</span><span class="sxs-lookup"><span data-stu-id="c14e0-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c14e0-109">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="c14e0-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="c14e0-110">ASP.NET Core uygulaması barındırabilen bir Web sunucusu gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="c14e0-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c14e0-111">Visual Studio, \*\* Blazor sunucu uygulaması\*\* proje şablonunu ( `blazorserverside` komut kullanılırken şablon) içerir [`dotnet new`](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="c14e0-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="c14e0-112">Ölçeklenebilirlik</span><span class="sxs-lookup"><span data-stu-id="c14e0-112">Scalability</span></span>

<span data-ttu-id="c14e0-113">Bir sunucu uygulaması için kullanılabilir altyapıyı en iyi şekilde kullanmasını sağlamak üzere bir dağıtım planlayın Blazor .</span><span class="sxs-lookup"><span data-stu-id="c14e0-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="c14e0-114">Sunucu uygulaması ölçeklenebilirliğini karşılamak için aşağıdaki kaynaklara bakın Blazor :</span><span class="sxs-lookup"><span data-stu-id="c14e0-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="c14e0-115">[BlazorSunucu uygulamalarının temelleri](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="c14e0-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="c14e0-116">Dağıtım sunucusu</span><span class="sxs-lookup"><span data-stu-id="c14e0-116">Deployment server</span></span>

<span data-ttu-id="c14e0-117">Tek bir sunucunun ölçeklenebilirliğini değerlendirirken (ölçeği büyütme), bir uygulama için kullanılabilir olan bellek büyük olasılıkla uygulamanın kullanıcı talebi arttıkça arttırabileceği ilk kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="c14e0-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="c14e0-118">Sunucudaki kullanılabilir bellek şunları etkiler:</span><span class="sxs-lookup"><span data-stu-id="c14e0-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="c14e0-119">Bir sunucunun destekleyebileceği etkin devre sayısı.</span><span class="sxs-lookup"><span data-stu-id="c14e0-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="c14e0-120">İstemcide UI gecikme süresi.</span><span class="sxs-lookup"><span data-stu-id="c14e0-120">UI latency on the client.</span></span>

<span data-ttu-id="c14e0-121">Güvenli ve ölçeklenebilir sunucu uygulamaları oluşturma hakkında yönergeler için Blazor bkz <xref:blazor/security/server/threat-mitigation> ..</span><span class="sxs-lookup"><span data-stu-id="c14e0-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="c14e0-122">Her bağlantı hattı en düşük *Merhaba Dünya*stilinde bir uygulama için YAKLAŞıK 250 KB bellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="c14e0-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="c14e0-123">Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c14e0-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="c14e0-124">Uygulama ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki taban çizgisi, dağıtım hedefini planlamada bir başlangıç noktası olabilir: uygulamanızın 5.000 eşzamanlı kullanıcı desteklemesini istiyorsanız, en az 1,3 GB sunucu belleğini uygulamaya (veya Kullanıcı başına ~ 273 KB) göre bütçeleme yapmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="c14e0-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="c14e0-125">yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="c14e0-125"> configuration</span></span>

Blazor<span data-ttu-id="c14e0-126">Sunucu uygulamaları SignalR , tarayıcıyla iletişim kurmak için ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="c14e0-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="c14e0-127">[ SignalR uygulamasının barındırma ve ölçeklendirme koşulları](xref:signalr/publish-to-azure-web-app) için Blazor geçerlidir Sunucu uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="c14e0-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="c14e0-128">SignalRdaha düşük gecikme süresi, güvenilirlik ve [güvenlik](xref:signalr/security)nedeniyle taşıma olarak WebSockets kullanırken en iyi şekilde işe yarar.</span><span class="sxs-lookup"><span data-stu-id="c14e0-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="c14e0-129">Uzun yoklama, SignalR WebSockets kullanılamadığında veya uygulama açıkça uzun yoklamayı kullanacak şekilde yapılandırıldığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c14e0-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="c14e0-130">Azure App Service dağıtım sırasında, uygulamayı hizmetin Azure portal ayarları içinde kullanmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c14e0-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="c14e0-131">Azure App Service için uygulamayı yapılandırma hakkında ayrıntılı bilgi için bkz. [ SignalR yayımlama yönergeleri](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="c14e0-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="c14e0-132">Azure SignalR hizmeti</span><span class="sxs-lookup"><span data-stu-id="c14e0-132">Azure SignalR Service</span></span>

<span data-ttu-id="c14e0-133">Sunucu uygulamaları için [Azure SignalR hizmetini](/azure/azure-signalr) kullanmanızı öneririz Blazor .</span><span class="sxs-lookup"><span data-stu-id="c14e0-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="c14e0-134">Hizmet, bir Blazor sunucu uygulamasının ölçeğini çok sayıda eşzamanlı bağlantıya ölçeklendirmeye olanak tanır SignalR .</span><span class="sxs-lookup"><span data-stu-id="c14e0-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="c14e0-135">Ayrıca, SignalR hizmetin küresel erişim ve yüksek performanslı veri merkezleri Coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="c14e0-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="c14e0-136">Azure hizmetini bir uygulamayı yapılandırmak (ve isteğe bağlı olarak sağlamak) için SignalR :</span><span class="sxs-lookup"><span data-stu-id="c14e0-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="c14e0-137">[Prerendering sırasında istemciler aynı sunucuya geri yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları*desteklemek için hizmeti etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c14e0-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="c14e0-138">`ServerStickyMode`Seçeneğini veya yapılandırma değerini olarak ayarlayın `Required` .</span><span class="sxs-lookup"><span data-stu-id="c14e0-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="c14e0-139">Genellikle, bir uygulama aşağıdaki yaklaşımlardan **birini** kullanarak yapılandırmayı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c14e0-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="c14e0-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c14e0-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="c14e0-141">Yapılandırma (aşağıdaki yaklaşımlardan **birini** kullanın):</span><span class="sxs-lookup"><span data-stu-id="c14e0-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="c14e0-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c14e0-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="c14e0-143">App Service 'in **Configuration**  >  Azure Portal (**ad**:, değer:) yapılandırma**uygulaması ayarları** `Azure:SignalR:ServerStickyMode` **Value** `Required` .</span><span class="sxs-lookup"><span data-stu-id="c14e0-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="c14e0-144">Sunucu uygulaması için Visual Studio 'da bir Azure Apps yayımlama profili oluşturun Blazor .</span><span class="sxs-lookup"><span data-stu-id="c14e0-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="c14e0-145">**Azure SignalR hizmet** bağımlılığını profile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e0-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="c14e0-146">Azure aboneliğinin uygulamaya atanacak önceden mevcut bir Azure SignalR hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **Yeni bir Azure SignalR hizmet örneği oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e0-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="c14e0-147">Uygulamayı Azure’da yayımlama.</span><span class="sxs-lookup"><span data-stu-id="c14e0-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="c14e0-148">IIS</span><span class="sxs-lookup"><span data-stu-id="c14e0-148">IIS</span></span>

<span data-ttu-id="c14e0-149">IIS kullanırken şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="c14e0-149">When using IIS, enable:</span></span>

* <span data-ttu-id="c14e0-150">[IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c14e0-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="c14e0-151">[Uygulama Isteği yönlendirme Ile yapışkan oturumlar](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="c14e0-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="c14e0-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="c14e0-152">Kubernetes</span></span>

<span data-ttu-id="c14e0-153">[Yapışkan oturumlar için aşağıdaki Kubernetes ek açıklamalarını](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)içeren bir giriş tanımı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c14e0-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="c14e0-154">Nginx ile Linux</span><span class="sxs-lookup"><span data-stu-id="c14e0-154">Linux with Nginx</span></span>

<span data-ttu-id="c14e0-155">SignalRWebSockets 'in düzgün çalışması için, proxy 'nin `Upgrade` ve `Connection` üst bilgilerinin aşağıdaki değerlere ayarlandığını ve şu değerlere eşlenmiş olduğunu doğrulayın `$connection_upgrade` :</span><span class="sxs-lookup"><span data-stu-id="c14e0-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="c14e0-156">Varsayılan olarak yükseltme üst bilgisi değeri.</span><span class="sxs-lookup"><span data-stu-id="c14e0-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="c14e0-157">`close`Yükseltme üst bilgisi eksik veya boş.</span><span class="sxs-lookup"><span data-stu-id="c14e0-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="c14e0-158">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c14e0-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="c14e0-159">WebSocket proxy 'Si olarak NGıNX</span><span class="sxs-lookup"><span data-stu-id="c14e0-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="c14e0-160">WebSocket proxy</span><span class="sxs-lookup"><span data-stu-id="c14e0-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="c14e0-161">Apache ile Linux</span><span class="sxs-lookup"><span data-stu-id="c14e0-161">Linux with Apache</span></span>

<span data-ttu-id="c14e0-162">BlazorLinux 'Ta Apache 'nin arkasındaki bir uygulamayı barındırmak için, `ProxyPass` http ve WebSockets trafiği için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c14e0-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="c14e0-163">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="c14e0-163">In the following example:</span></span>

* <span data-ttu-id="c14e0-164">Kestrel sunucusu konak makinede çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="c14e0-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="c14e0-165">Uygulama, 5000 numaralı bağlantı noktasında trafiği dinler.</span><span class="sxs-lookup"><span data-stu-id="c14e0-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="c14e0-166">Aşağıdaki modülleri etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="c14e0-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="c14e0-167">Tarayıcı konsolunda WebSockets hataları olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e0-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="c14e0-168">Örnek hatalar:</span><span class="sxs-lookup"><span data-stu-id="c14e0-168">Example errors:</span></span>

* <span data-ttu-id="c14e0-169">Firefox, ws://the-domain-name.tld/_blazor?id=XXX adresinde sunucuyla bağlantı kuramıyor.</span><span class="sxs-lookup"><span data-stu-id="c14e0-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="c14e0-170">Hata: ' WebSockets ' taşıması başlatılamadı: hata: aktarımda bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="c14e0-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="c14e0-171">Hata: ' LongPolling ' taşıması başlatılamadı: TypeError: this. Transport tanımsız</span><span class="sxs-lookup"><span data-stu-id="c14e0-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="c14e0-172">Hata: kullanılabilir aktarımlarla sunucuya bağlanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="c14e0-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="c14e0-173">WebSockets başarısız oldu</span><span class="sxs-lookup"><span data-stu-id="c14e0-173">WebSockets failed</span></span>
* <span data-ttu-id="c14e0-174">Hata: bağlantı ' bağlı ' durumunda değilse veri gönderilemez.</span><span class="sxs-lookup"><span data-stu-id="c14e0-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="c14e0-175">Daha fazla bilgi için [Apache belgelerine](https://httpd.apache.org/docs/current/mod/mod_proxy.html)bakın.</span><span class="sxs-lookup"><span data-stu-id="c14e0-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="c14e0-176">Ölçü ağı gecikmesi</span><span class="sxs-lookup"><span data-stu-id="c14e0-176">Measure network latency</span></span>

<span data-ttu-id="c14e0-177">Aşağıdaki örnekte gösterildiği gibi, [js birlikte çalışması](xref:blazor/call-javascript-from-dotnet) ağ gecikmesini ölçmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c14e0-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="c14e0-178">Makul bir kullanıcı arabirimi deneyimi için, 250ms veya daha az sayıda sürekli Kullanıcı arabirimi gecikme süresi önerilir.</span><span class="sxs-lookup"><span data-stu-id="c14e0-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
