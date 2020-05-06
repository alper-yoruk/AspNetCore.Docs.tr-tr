---
title: ASP.NET Core Blazor sunucusu barındırma ve dağıtma
author: guardrex
description: ASP.NET Core kullanarak Blazor sunucu uygulamasını nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e69b91035c65739dde724330e83793c0b8b5481a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775160"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="f5610-103">Sunucu barındırma ve Blazor dağıtma</span><span class="sxs-lookup"><span data-stu-id="f5610-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="f5610-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="f5610-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f5610-105">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="f5610-105">Host configuration values</span></span>

<span data-ttu-id="f5610-106">Sunucu uygulamaları [genel ana bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="f5610-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="f5610-107">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="f5610-107">Deployment</span></span>

<span data-ttu-id="f5610-108">Sunucu barındırma modelinin kullanımı, Blazor sunucuda bir ASP.NET Core uygulamasının içinden yürütülür. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="f5610-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="f5610-109">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir [SignalR](xref:signalr/introduction) bağlantı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="f5610-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="f5610-110">ASP.NET Core uygulaması barındırabilen bir Web sunucusu gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="f5610-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="f5610-111">Visual Studio, \*\* Blazor sunucu uygulaması\*\* proje şablonunu (`blazorserverside` [DotNet New](/dotnet/core/tools/dotnet-new) komutu kullanılırken şablon) içerir.</span><span class="sxs-lookup"><span data-stu-id="f5610-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="f5610-112">Ölçeklenebilirlik</span><span class="sxs-lookup"><span data-stu-id="f5610-112">Scalability</span></span>

<span data-ttu-id="f5610-113">Bir Blazor sunucu uygulaması için kullanılabilir altyapıyı en iyi şekilde kullanmasını sağlamak üzere bir dağıtım planlayın.</span><span class="sxs-lookup"><span data-stu-id="f5610-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="f5610-114">Sunucu uygulaması ölçeklenebilirliğini karşılamak Blazor için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="f5610-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="f5610-115">[Blazor Sunucu uygulamalarının temelleri](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="f5610-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="f5610-116">Dağıtım sunucusu</span><span class="sxs-lookup"><span data-stu-id="f5610-116">Deployment server</span></span>

<span data-ttu-id="f5610-117">Tek bir sunucunun ölçeklenebilirliğini değerlendirirken (ölçeği büyütme), bir uygulama için kullanılabilir olan bellek büyük olasılıkla uygulamanın kullanıcı talebi arttıkça arttırabileceği ilk kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="f5610-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="f5610-118">Sunucudaki kullanılabilir bellek şunları etkiler:</span><span class="sxs-lookup"><span data-stu-id="f5610-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="f5610-119">Bir sunucunun destekleyebileceği etkin devre sayısı.</span><span class="sxs-lookup"><span data-stu-id="f5610-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="f5610-120">İstemcide UI gecikme süresi.</span><span class="sxs-lookup"><span data-stu-id="f5610-120">UI latency on the client.</span></span>

<span data-ttu-id="f5610-121">Güvenli ve ölçeklenebilir Blazor sunucu uygulamaları oluşturma hakkında yönergeler için bkz <xref:security/blazor/server/threat-mitigation>..</span><span class="sxs-lookup"><span data-stu-id="f5610-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="f5610-122">Her bağlantı hattı en düşük *Merhaba Dünya*stilinde bir uygulama için YAKLAŞıK 250 KB bellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5610-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="f5610-123">Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f5610-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="f5610-124">Uygulama ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki taban çizgisi, dağıtım hedefini planlamada bir başlangıç noktası olabilir: uygulamanızın 5.000 eşzamanlı kullanıcı desteklemesini istiyorsanız, en az 1,3 GB sunucu belleğini uygulamaya (veya Kullanıcı başına ~ 273 KB) göre bütçeleme yapmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="f5610-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="f5610-125">yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="f5610-125"> configuration</span></span>

Blazor<span data-ttu-id="f5610-126">Sunucu uygulamaları, tarayıcıyla SignalR iletişim kurmak için ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5610-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="f5610-127">[uygulamasının barındırma ve ölçeklendirme koşulları, sunucu uygulamaları için geçerlidir. SignalR](xref:signalr/publish-to-azure-web-app) Blazor</span><span class="sxs-lookup"><span data-stu-id="f5610-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="f5610-128">daha düşük gecikme süresi, güvenilirlik ve SignalR [güvenlik](xref:signalr/security)nedeniyle taşıma olarak WebSockets kullanırken en iyi şekilde işe yarar.</span><span class="sxs-lookup"><span data-stu-id="f5610-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="f5610-129">Uzun yoklama, WebSockets kullanılamadığında SignalR veya uygulama açıkça uzun yoklamayı kullanacak şekilde yapılandırıldığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f5610-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="f5610-130">Azure App Service dağıtım sırasında, uygulamayı hizmetin Azure portal ayarları içinde kullanmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f5610-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="f5610-131">Azure App Service için uygulamayı yapılandırma hakkında ayrıntılı bilgi için bkz. [ SignalR yayımlama yönergeleri](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="f5610-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="f5610-132">Azure SignalR hizmeti</span><span class="sxs-lookup"><span data-stu-id="f5610-132">Azure SignalR Service</span></span>

<span data-ttu-id="f5610-133">Sunucu uygulamaları için Blazor [ SignalR Azure hizmetini](/azure/azure-signalr) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f5610-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="f5610-134">Hizmet, bir Blazor sunucu uygulamasının ölçeğini çok sayıda eşzamanlı SignalR bağlantıya ölçeklendirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f5610-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="f5610-135">Ayrıca, SignalR hizmetin küresel erişim ve yüksek performanslı veri merkezleri Coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="f5610-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="f5610-136">Azure SignalR hizmetini bir uygulamayı yapılandırmak (ve isteğe bağlı olarak sağlamak) için:</span><span class="sxs-lookup"><span data-stu-id="f5610-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="f5610-137">[Prerendering sırasında istemciler aynı sunucuya geri yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları*desteklemek için hizmeti etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f5610-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="f5610-138">`ServerStickyMode` Seçeneğini veya yapılandırma değerini olarak `Required`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f5610-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="f5610-139">Genellikle, bir uygulama aşağıdaki yaklaşımlardan **birini** kullanarak yapılandırmayı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f5610-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="f5610-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f5610-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="f5610-141">Yapılandırma (aşağıdaki yaklaşımlardan **birini** kullanın):</span><span class="sxs-lookup"><span data-stu-id="f5610-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="f5610-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f5610-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="f5610-143">App Service 'in Azure Portal (**ad** `Azure:SignalR:ServerStickyMode`:, **değer**: `Required`) **yapılandırma** > **uygulaması ayarları** .</span><span class="sxs-lookup"><span data-stu-id="f5610-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="f5610-144">Blazor Sunucu uygulaması Için Visual Studio 'Da bir Azure Apps yayımlama profili oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f5610-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="f5610-145">**Azure SignalR hizmet** bağımlılığını profile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5610-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="f5610-146">Azure aboneliğinin uygulamaya atanacak önceden mevcut bir Azure SignalR hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **Yeni bir Azure SignalR hizmet örneği oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f5610-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="f5610-147">Uygulamayı Azure’da yayımlama.</span><span class="sxs-lookup"><span data-stu-id="f5610-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="f5610-148">IIS</span><span class="sxs-lookup"><span data-stu-id="f5610-148">IIS</span></span>

<span data-ttu-id="f5610-149">IIS kullanırken şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="f5610-149">When using IIS, enable:</span></span>

* <span data-ttu-id="f5610-150">[IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="f5610-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="f5610-151">[Uygulama Isteği yönlendirme Ile yapışkan oturumlar](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="f5610-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="f5610-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="f5610-152">Kubernetes</span></span>

<span data-ttu-id="f5610-153">[Yapışkan oturumlar için aşağıdaki Kubernetes ek açıklamalarını](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)içeren bir giriş tanımı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f5610-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="f5610-154">Nginx ile Linux</span><span class="sxs-lookup"><span data-stu-id="f5610-154">Linux with Nginx</span></span>

<span data-ttu-id="f5610-155">WebSockets SignalR 'in düzgün çalışması için, proxy 'nin `Upgrade` ve `Connection` üst bilgilerinin aşağıdaki değerlere ayarlandığını ve `$connection_upgrade` şu değerlere eşlenmiş olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f5610-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="f5610-156">Varsayılan olarak yükseltme üst bilgisi değeri.</span><span class="sxs-lookup"><span data-stu-id="f5610-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="f5610-157">`close`Yükseltme üst bilgisi eksik veya boş.</span><span class="sxs-lookup"><span data-stu-id="f5610-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="f5610-158">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="f5610-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="f5610-159">WebSocket proxy 'Si olarak NGıNX</span><span class="sxs-lookup"><span data-stu-id="f5610-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="f5610-160">WebSocket proxy</span><span class="sxs-lookup"><span data-stu-id="f5610-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="f5610-161">Ölçü ağı gecikmesi</span><span class="sxs-lookup"><span data-stu-id="f5610-161">Measure network latency</span></span>

<span data-ttu-id="f5610-162">Aşağıdaki örnekte gösterildiği gibi, [js birlikte çalışması](xref:blazor/call-javascript-from-dotnet) ağ gecikmesini ölçmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f5610-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="f5610-163">Makul bir kullanıcı arabirimi deneyimi için, 250ms veya daha az sayıda sürekli Kullanıcı arabirimi gecikme süresi önerilir.</span><span class="sxs-lookup"><span data-stu-id="f5610-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
