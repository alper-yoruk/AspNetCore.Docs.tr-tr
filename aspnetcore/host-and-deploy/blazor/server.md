---
title: Core Blazor Server'ı barındırma ve dağıtma ASP.NET
author: guardrex
description: ASP.NET Core'u kullanarak Blazor bir Sunucu uygulamasını nasıl barındırıp dağıttığınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025420"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="33004-103">Sunucu'ya Blazor ana bilgisayar ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="33004-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="33004-104">Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="33004-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="33004-105">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="33004-105">Host configuration values</span></span>

<span data-ttu-id="33004-106">Sunucu uygulamaları [Genel Ana Bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="33004-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="33004-107">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="33004-107">Deployment</span></span>

<span data-ttu-id="33004-108">Sunucu barındırma modeli Blazor kullanılarak, sunucuda bir ASP.NET Core uygulaması içinden yürütülür. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="33004-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="33004-109">UI güncelleştirmeleri, olay işleme ve JavaScript [SignalR](xref:signalr/introduction) çağrıları bir bağlantı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="33004-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="33004-110">ASP.NET Core uygulaması na sahip bir web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="33004-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="33004-111">Visual \*\* Blazor Studio, Sunucu Uygulaması\*\* `blazorserverside` proje şablonunu [(dotnet yeni](/dotnet/core/tools/dotnet-new) komutunu kullanırken şablon) içerir.</span><span class="sxs-lookup"><span data-stu-id="33004-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="33004-112">Ölçeklenebilirlik</span><span class="sxs-lookup"><span data-stu-id="33004-112">Scalability</span></span>

<span data-ttu-id="33004-113">Bir Blazor Sunucu uygulaması için kullanılabilir altyapıdan en iyi şekilde yararlanmak için bir dağıtım planlayın.</span><span class="sxs-lookup"><span data-stu-id="33004-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="33004-114">Sunucu uygulaması ölçeklenebilirliğini ele Blazor almak için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="33004-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="33004-115">[Sunucu uygulamalarının Blazor temelleri](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="33004-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="33004-116">Dağıtım sunucusu</span><span class="sxs-lookup"><span data-stu-id="33004-116">Deployment server</span></span>

<span data-ttu-id="33004-117">Tek bir sunucunun ölçeklenebilirliği göz önüne alındığında (ölçeklendirin), bir uygulamanın kullanabileceği bellek, kullanıcı talepleri arttıkça uygulamanın tükeceği ilk kaynak tır.</span><span class="sxs-lookup"><span data-stu-id="33004-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="33004-118">Sunucudaki kullanılabilir bellek aşağıdakileri etkiler:</span><span class="sxs-lookup"><span data-stu-id="33004-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="33004-119">Bir sunucunun destekleyebilir etkin devrelerin sayısı.</span><span class="sxs-lookup"><span data-stu-id="33004-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="33004-120">Müşteride ui gecikmesi.</span><span class="sxs-lookup"><span data-stu-id="33004-120">UI latency on the client.</span></span>

<span data-ttu-id="33004-121">Güvenli ve ölçeklenebilir Blazor sunucu uygulamaları oluşturma <xref:security/blazor/server>kılavuzu için bkz.</span><span class="sxs-lookup"><span data-stu-id="33004-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="33004-122">Her devre, en az *Merhaba World*tarzı uygulama için yaklaşık 250 KB bellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="33004-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="33004-123">Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="33004-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="33004-124">Uygulamanız ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki temel, dağıtım hedefinizi planlamada bir başlangıç noktası olabilir: Uygulamanızın 5.000 eşzamanlı kullanıcıyı desteklemesini bekliyorsanız, uygulamaya en az 1,3 GB sunucu belleği (veya kullanıcı başına ~273 KB) bütçeleme yapmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="33004-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="33004-125">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="33004-125"> configuration</span></span>

Blazor<span data-ttu-id="33004-126">Sunucu uygulamaları tarayıcı SignalR yla iletişim kurmak için ASP.NET Core'u kullanır.</span><span class="sxs-lookup"><span data-stu-id="33004-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="33004-127">Sunucu uygulamaları için Blazor [barındırma ve ölçekleme koşulları geçerlidir. SignalR](xref:signalr/publish-to-azure-web-app)</span><span class="sxs-lookup"><span data-stu-id="33004-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="33004-128">daha düşük gecikme, güvenilirlik SignalR ve [güvenlik](xref:signalr/security)nedeniyle aktarım olarak WebSockets kullanırken en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="33004-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="33004-129">Uzun Yoklama, SignalR WebSockets'in kullanılamadığı veya uygulamanın açıkça Uzun Yoklama'yı kullanacak şekilde yapılandırıldığı zaman kullanılır.</span><span class="sxs-lookup"><span data-stu-id="33004-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="33004-130">Azure Uygulama Hizmeti'ne dağıtılırken, uygulamayı hizmetiçin Azure portal ayarlarında WebSockets kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="33004-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="33004-131">Azure Uygulama Hizmeti için uygulamayı yapılandırma hakkında ayrıntılı bilgi için [ SignalR yayımlama yönergelerine](xref:signalr/publish-to-azure-web-app)bakın.</span><span class="sxs-lookup"><span data-stu-id="33004-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="33004-132">Azure SignalR Hizmeti</span><span class="sxs-lookup"><span data-stu-id="33004-132">Azure SignalR Service</span></span>

<span data-ttu-id="33004-133">Sunucu uygulamaları için Blazor [ SignalR Azure Hizmetini](/azure/azure-signalr) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="33004-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="33004-134">Bu hizmet, bir Blazor Server uygulamasını çok sayıda eşzamanlı SignalR bağlantıya ölçeklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="33004-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="33004-135">Buna ek SignalR olarak, hizmetin küresel erişimi ve yüksek performanslı veri merkezleri, coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı dır.</span><span class="sxs-lookup"><span data-stu-id="33004-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="33004-136">Bir uygulamayı yapılandırmak (ve isteğe SignalR bağlı olarak sağlama) Azure Hizmeti için:</span><span class="sxs-lookup"><span data-stu-id="33004-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="33004-137">Hizmetin, istemcilerin [önceden işletilirken aynı sunucuya yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları*desteklemesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="33004-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="33004-138">`ServerStickyMode` Seçeneği veya yapılandırma değerini `Required`' olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="33004-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="33004-139">Genellikle, bir uygulama aşağıdaki **yaklaşımlardan birini** kullanarak yapılandırmayı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="33004-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="33004-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="33004-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="33004-141">Yapılandırma (aşağıdaki **yaklaşımlardan birini** kullanın):</span><span class="sxs-lookup"><span data-stu-id="33004-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="33004-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="33004-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="33004-143">Uygulama hizmetinin Azure portalındaki **Yapılandırma** > **Uygulaması ayarları** ( `Required`**Ad**: , `Azure:SignalR:ServerStickyMode` **Değer**: ).</span><span class="sxs-lookup"><span data-stu-id="33004-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="33004-144">Blazor Sunucu uygulaması için Visual Studio'da bir Azure Apps yayımlama profili oluşturun.</span><span class="sxs-lookup"><span data-stu-id="33004-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="33004-145">Profile **Azure SignalR Hizmeti** bağımlılığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33004-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="33004-146">Azure aboneliğinde uygulamaya atamak için önceden SignalR varolan bir Azure Hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **yeni bir Azure SignalR Hizmeti örneği oluştur'u** seçin.</span><span class="sxs-lookup"><span data-stu-id="33004-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="33004-147">Uygulamayı Azure’da yayımlama.</span><span class="sxs-lookup"><span data-stu-id="33004-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="33004-148">IIS</span><span class="sxs-lookup"><span data-stu-id="33004-148">IIS</span></span>

<span data-ttu-id="33004-149">IIS kullanırken şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="33004-149">When using IIS, enable:</span></span>

* <span data-ttu-id="33004-150">[IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="33004-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="33004-151">[Uygulama İsteği Yönlendirme ile yapışkan oturumlar.](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="33004-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="33004-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="33004-152">Kubernetes</span></span>

<span data-ttu-id="33004-153">Yapışkan oturumlar için aşağıdaki [Kubernetes ek açıklamaları](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)ile bir giriş tanımı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33004-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="33004-154">Nginx ile Linux</span><span class="sxs-lookup"><span data-stu-id="33004-154">Linux with Nginx</span></span>

<span data-ttu-id="33004-155">SignalR WebSockets'lerin düzgün çalışması için proxy'nin `Upgrade` `Connection` ve üstbilginin aşağıdaki değerlere `$connection_upgrade` ayarladığını ve bu değerlerin hangisine eşlenediğini onaylayın:</span><span class="sxs-lookup"><span data-stu-id="33004-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="33004-156">Yükseltme üstbilgi değeri varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="33004-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="33004-157">`close`Yükseltme üstbilgisi eksik veya boş olduğunda.</span><span class="sxs-lookup"><span data-stu-id="33004-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="33004-158">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="33004-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="33004-159">Bir WebSocket Proxy olarak NGINX</span><span class="sxs-lookup"><span data-stu-id="33004-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="33004-160">WebSocket proxying</span><span class="sxs-lookup"><span data-stu-id="33004-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="33004-161">Ağ gecikmesi ölçme</span><span class="sxs-lookup"><span data-stu-id="33004-161">Measure network latency</span></span>

<span data-ttu-id="33004-162">[JS interop](xref:blazor/call-javascript-from-dotnet) aşağıdaki örnekte gösterildiği gibi, ağ gecikmesini ölçmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="33004-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="33004-163">Makul bir UI deneyimi için, 250ms veya daha az sürekli bir UI gecikmesi öneririz.</span><span class="sxs-lookup"><span data-stu-id="33004-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
