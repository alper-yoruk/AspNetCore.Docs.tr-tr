---
title: 'ASP.NET Core barındırma ve dağıtma :::no-loc(Blazor Server):::'
author: guardrex
description: 'ASP.NET Core kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin :::no-loc(Blazor Server)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595447"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="4fa32-103">Barındırma ve dağıtma :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="4fa32-103">Host and deploy :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="4fa32-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="4fa32-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4fa32-105">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="4fa32-105">Host configuration values</span></span>

<span data-ttu-id="4fa32-106">[ :::no-loc(Blazor Server)::: uygulamalar](xref:blazor/hosting-models#blazor-server) [genel ana bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="4fa32-106">[:::no-loc(Blazor Server)::: apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="4fa32-107">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="4fa32-107">Deployment</span></span>

<span data-ttu-id="4fa32-108">[ :::no-loc(Blazor Server)::: Barındırma modelinin](xref:blazor/hosting-models#blazor-server)kullanımı, :::no-loc(Blazor)::: sunucuda bir ASP.NET Core uygulamasının içinden yürütülür.</span><span class="sxs-lookup"><span data-stu-id="4fa32-108">Using the [:::no-loc(Blazor Server)::: hosting model](xref:blazor/hosting-models#blazor-server), :::no-loc(Blazor)::: is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="4fa32-109">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir bağlantı üzerinden işlenir [:::no-loc(SignalR):::](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="4fa32-109">UI updates, event handling, and JavaScript calls are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="4fa32-110">ASP.NET Core uygulaması barındırabilen bir Web sunucusu gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="4fa32-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="4fa32-111">Visual Studio, **:::no-loc(Blazor Server)::: uygulama** proje şablonunu içerir ( `blazorserverside` komutunu kullanırken şablon [`dotnet new`](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="4fa32-111">Visual Studio includes the **:::no-loc(Blazor Server)::: App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="4fa32-112">Ölçeklenebilirlik</span><span class="sxs-lookup"><span data-stu-id="4fa32-112">Scalability</span></span>

<span data-ttu-id="4fa32-113">Bir uygulama için kullanılabilir altyapıyı en iyi şekilde kullanmasını sağlamak üzere bir dağıtım planlayın :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4fa32-113">Plan a deployment to make the best use of the available infrastructure for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="4fa32-114">Uygulama ölçeklenebilirliğini karşılamak için aşağıdaki kaynaklara bakın :::no-loc(Blazor Server)::: :</span><span class="sxs-lookup"><span data-stu-id="4fa32-114">See the following resources to address :::no-loc(Blazor Server)::: app scalability:</span></span>

* [<span data-ttu-id="4fa32-115">Uygulamaların temelleri :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="4fa32-115">Fundamentals of :::no-loc(Blazor Server)::: apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="4fa32-116">Dağıtım sunucusu</span><span class="sxs-lookup"><span data-stu-id="4fa32-116">Deployment server</span></span>

<span data-ttu-id="4fa32-117">Tek bir sunucunun ölçeklenebilirliğini değerlendirirken (ölçeği büyütme), bir uygulama için kullanılabilir olan bellek büyük olasılıkla uygulamanın kullanıcı talebi arttıkça arttırabileceği ilk kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="4fa32-118">Sunucudaki kullanılabilir bellek şunları etkiler:</span><span class="sxs-lookup"><span data-stu-id="4fa32-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="4fa32-119">Bir sunucunun destekleyebileceği etkin devre sayısı.</span><span class="sxs-lookup"><span data-stu-id="4fa32-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="4fa32-120">İstemcide UI gecikme süresi.</span><span class="sxs-lookup"><span data-stu-id="4fa32-120">UI latency on the client.</span></span>

<span data-ttu-id="4fa32-121">Güvenli ve ölçeklenebilir sunucu uygulamaları oluşturma hakkında yönergeler için :::no-loc(Blazor)::: bkz <xref:blazor/security/server/threat-mitigation> ..</span><span class="sxs-lookup"><span data-stu-id="4fa32-121">For guidance on building secure and scalable :::no-loc(Blazor)::: server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="4fa32-122">Her bağlantı hattı en düşük *Merhaba Dünya* stilinde bir uygulama için YAKLAŞıK 250 KB bellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World* -style app.</span></span> <span data-ttu-id="4fa32-123">Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="4fa32-124">Uygulama ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki taban çizgisi, dağıtım hedefini planlamada bir başlangıç noktası olabilir: uygulamanızın 5.000 eşzamanlı kullanıcı desteklemesini istiyorsanız, en az 1,3 GB sunucu belleğini uygulamaya (veya Kullanıcı başına ~ 273 KB) göre bütçeleme yapmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="4fa32-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="4fa32-125">:::no-loc(SignalR)::: yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="4fa32-125">:::no-loc(SignalR)::: configuration</span></span>

<span data-ttu-id="4fa32-126">:::no-loc(Blazor Server)::: uygulamalar :::no-loc(SignalR)::: tarayıcıyla iletişim kurmak için ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-126">:::no-loc(Blazor Server)::: apps use ASP.NET Core :::no-loc(SignalR)::: to communicate with the browser.</span></span> <span data-ttu-id="4fa32-127">[ :::no-loc(SignalR)::: uygulamasının barındırma ve ölçeklendirme koşulları](xref:signalr/publish-to-azure-web-app) uygulamalar için geçerlidir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4fa32-127">[:::no-loc(SignalR):::'s hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to :::no-loc(Blazor Server)::: apps.</span></span>

<span data-ttu-id="4fa32-128">:::no-loc(Blazor)::::::no-loc(SignalR):::daha düşük gecikme süresi, güvenilirlik ve [güvenlik](xref:signalr/security)nedeniyle taşıma olarak WebSockets kullanırken en iyi şekilde işe yarar.</span><span class="sxs-lookup"><span data-stu-id="4fa32-128">:::no-loc(Blazor)::: works best when using WebSockets as the :::no-loc(SignalR)::: transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="4fa32-129">Uzun yoklama, :::no-loc(SignalR)::: WebSockets kullanılamadığında veya uygulama açıkça uzun yoklamayı kullanacak şekilde yapılandırıldığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-129">Long Polling is used by :::no-loc(SignalR)::: when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="4fa32-130">Azure App Service dağıtım sırasında, uygulamayı hizmetin Azure portal ayarları içinde kullanmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4fa32-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="4fa32-131">Azure App Service için uygulamayı yapılandırma hakkında ayrıntılı bilgi için bkz. [ :::no-loc(SignalR)::: yayımlama yönergeleri](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="4fa32-131">For details on configuring the app for Azure App Service, see the [:::no-loc(SignalR)::: publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="4fa32-132">Azure :::no-loc(SignalR)::: hizmeti</span><span class="sxs-lookup"><span data-stu-id="4fa32-132">Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="4fa32-133">Uygulamalar için [Azure :::no-loc(SignalR)::: hizmetini](xref:signalr/scale#azure-signalr-service) kullanmanızı öneririz :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4fa32-133">We recommend using the [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) for :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="4fa32-134">Hizmet, bir :::no-loc(Blazor Server)::: uygulamayı çok sayıda eşzamanlı bağlantıya ölçeklendirmeye olanak tanır :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="4fa32-134">The service allows for scaling up a :::no-loc(Blazor Server)::: app to a large number of concurrent :::no-loc(SignalR)::: connections.</span></span> <span data-ttu-id="4fa32-135">Ayrıca, :::no-loc(SignalR)::: hizmetin küresel erişim ve yüksek performanslı veri merkezleri Coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="4fa32-135">In addition, the :::no-loc(SignalR)::: service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4fa32-136">[WebSockets](https://wikipedia.org/wiki/WebSocket) devre dışı bırakıldığında, Azure App Service http uzun yoklama kullanarak gerçek zamanlı bir bağlantıyı taklit eder.</span><span class="sxs-lookup"><span data-stu-id="4fa32-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="4fa32-137">HTTP uzun yoklama, WebSockets etkinken, bir istemci-sunucu bağlantısının benzetimini yapmak için yoklama kullanmayan, önemli ölçüde daha yavaştır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="4fa32-138">:::no-loc(Blazor Server):::Azure App Service için dağıtılan uygulamalar Için WebSockets kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4fa32-138">We recommend using WebSockets for :::no-loc(Blazor Server)::: apps deployed to Azure App Service.</span></span> <span data-ttu-id="4fa32-139">[Azure :::no-loc(SignalR)::: hizmeti](xref:signalr/scale#azure-signalr-service) varsayılan olarak WebSockets kullanır.</span><span class="sxs-lookup"><span data-stu-id="4fa32-139">The [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="4fa32-140">Uygulama Azure :::no-loc(SignalR)::: hizmetini kullanmıyorsa, bkz <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> ..</span><span class="sxs-lookup"><span data-stu-id="4fa32-140">If the app doesn't use the Azure :::no-loc(SignalR)::: Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="4fa32-141">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="4fa32-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="4fa32-142">Azure hizmeti nedir :::no-loc(SignalR)::: ?</span><span class="sxs-lookup"><span data-stu-id="4fa32-142">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="4fa32-143">Azure hizmeti için performans Kılavuzu :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="4fa32-143">Performance guide for Azure :::no-loc(SignalR)::: Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="4fa32-144">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4fa32-144">Configuration</span></span>

<span data-ttu-id="4fa32-145">Azure hizmeti için bir uygulamayı yapılandırmak üzere :::no-loc(SignalR)::: , uygulamanın [prerendering sırasında aynı sunucuya geri yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları* desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4fa32-145">To configure an app for the Azure :::no-loc(SignalR)::: Service, the app must support *sticky sessions* , where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="4fa32-146">`ServerStickyMode`Seçenek veya yapılandırma değeri olarak ayarlanır `Required` .</span><span class="sxs-lookup"><span data-stu-id="4fa32-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="4fa32-147">Genellikle, bir uygulama aşağıdaki yaklaşımlardan **_birini_** kullanarak yapılandırmayı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4fa32-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="4fa32-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4fa32-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.Add:::no-loc(SignalR):::().AddAzure:::no-loc(SignalR):::(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.:::no-loc(SignalR):::.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="4fa32-149">Yapılandırma (aşağıdaki yaklaşımlardan **_birini_** kullanın):</span><span class="sxs-lookup"><span data-stu-id="4fa32-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="4fa32-150">`:::no-loc(appsettings.json):::` içinde:</span><span class="sxs-lookup"><span data-stu-id="4fa32-150">In `:::no-loc(appsettings.json):::`:</span></span>

       ```json
       "Azure::::no-loc(SignalR)::::StickyServerMode": "Required"
       ```

     * <span data-ttu-id="4fa32-151">App Service 'in **Configuration**  >  Azure Portal ( **ad** :, değer:) yapılandırma **uygulaması ayarları** `Azure__:::no-loc(SignalR):::__StickyServerMode` **Value** `Required` .</span><span class="sxs-lookup"><span data-stu-id="4fa32-151">The app service's **Configuration** > **Application settings** in the Azure portal ( **Name** : `Azure__:::no-loc(SignalR):::__StickyServerMode`, **Value** : `Required`).</span></span> <span data-ttu-id="4fa32-152">Bu yaklaşım, [Azure :::no-loc(SignalR)::: hizmetini temin](#provision-the-azure-signalr-service)ediyorsanız uygulama için otomatik olarak benimsenmiştir.</span><span class="sxs-lookup"><span data-stu-id="4fa32-152">This approach is adopted for the app automatically if you [provision the Azure :::no-loc(SignalR)::: Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-no-locsignalr-service"></a><span data-ttu-id="4fa32-153">Azure hizmetini sağlama :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="4fa32-153">Provision the Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="4fa32-154">:::no-loc(SignalR):::Visual Studio 'da bir uygulama Için Azure hizmetini sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="4fa32-154">To provision the Azure :::no-loc(SignalR)::: Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="4fa32-155">Uygulama için Visual Studio 'da bir Azure Apps yayımlama profili oluşturun :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="4fa32-155">Create an Azure Apps publish profile in Visual Studio for the :::no-loc(Blazor Server)::: app.</span></span>
1. <span data-ttu-id="4fa32-156">**Azure :::no-loc(SignalR)::: hizmet** bağımlılığını profile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4fa32-156">Add the **Azure :::no-loc(SignalR)::: Service** dependency to the profile.</span></span> <span data-ttu-id="4fa32-157">Azure aboneliğinin uygulamaya atanacak önceden mevcut bir Azure :::no-loc(SignalR)::: hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **Yeni bir Azure :::no-loc(SignalR)::: hizmet örneği oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="4fa32-157">If the Azure subscription doesn't have a pre-existing Azure :::no-loc(SignalR)::: Service instance to assign to the app, select **Create a new Azure :::no-loc(SignalR)::: Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="4fa32-158">Uygulamayı Azure’da yayımlama.</span><span class="sxs-lookup"><span data-stu-id="4fa32-158">Publish the app to Azure.</span></span>

<span data-ttu-id="4fa32-159">:::no-loc(SignalR):::Visual Studio 'Da Azure hizmeti sağlamak, otomatik [olarak *yapışkan oturumları* sağlar](#configuration) ve :::no-loc(SignalR)::: bağlantı dizesini App Service 'in yapılandırmasına ekler.</span><span class="sxs-lookup"><span data-stu-id="4fa32-159">Provisioning the Azure :::no-loc(SignalR)::: Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the :::no-loc(SignalR)::: connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="4fa32-160">IIS</span><span class="sxs-lookup"><span data-stu-id="4fa32-160">IIS</span></span>

<span data-ttu-id="4fa32-161">IIS kullanırken şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="4fa32-161">When using IIS, enable:</span></span>

* <span data-ttu-id="4fa32-162">[IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4fa32-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="4fa32-163">[Uygulama Isteği yönlendirme Ile yapışkan oturumlar](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="4fa32-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="4fa32-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="4fa32-164">Kubernetes</span></span>

<span data-ttu-id="4fa32-165">[Yapışkan oturumlar için aşağıdaki Kubernetes ek açıklamalarını](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/)içeren bir giriş tanımı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4fa32-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: ":::no-loc(cookie):::"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-name: "affinity"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-expires: "14400"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="4fa32-166">Nginx ile Linux</span><span class="sxs-lookup"><span data-stu-id="4fa32-166">Linux with Nginx</span></span>

<span data-ttu-id="4fa32-167">:::no-loc(SignalR):::WebSockets 'in düzgün çalışması için, proxy 'nin `Upgrade` ve `Connection` üst bilgilerinin aşağıdaki değerlere ayarlandığını ve şu değerlere eşlenmiş olduğunu doğrulayın `$connection_upgrade` :</span><span class="sxs-lookup"><span data-stu-id="4fa32-167">For :::no-loc(SignalR)::: WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="4fa32-168">Varsayılan olarak yükseltme üst bilgisi değeri.</span><span class="sxs-lookup"><span data-stu-id="4fa32-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="4fa32-169">`close` Yükseltme üst bilgisi eksik veya boş.</span><span class="sxs-lookup"><span data-stu-id="4fa32-169">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="4fa32-170">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="4fa32-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="4fa32-171">WebSocket proxy 'Si olarak NGıNX</span><span class="sxs-lookup"><span data-stu-id="4fa32-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="4fa32-172">WebSocket proxy</span><span class="sxs-lookup"><span data-stu-id="4fa32-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="4fa32-173">Apache ile Linux</span><span class="sxs-lookup"><span data-stu-id="4fa32-173">Linux with Apache</span></span>

<span data-ttu-id="4fa32-174">:::no-loc(Blazor):::Linux 'Ta Apache 'nin arkasındaki bir uygulamayı barındırmak için, `ProxyPass` http ve WebSockets trafiği için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4fa32-174">To host a :::no-loc(Blazor)::: app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="4fa32-175">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="4fa32-175">In the following example:</span></span>

* <span data-ttu-id="4fa32-176">Kestrel sunucusu konak makinede çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="4fa32-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="4fa32-177">Uygulama, 5000 numaralı bağlantı noktasında trafiği dinler.</span><span class="sxs-lookup"><span data-stu-id="4fa32-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="4fa32-178">Aşağıdaki modülleri etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="4fa32-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="4fa32-179">Tarayıcı konsolunda WebSockets hataları olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4fa32-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="4fa32-180">Örnek hatalar:</span><span class="sxs-lookup"><span data-stu-id="4fa32-180">Example errors:</span></span>

* <span data-ttu-id="4fa32-181">Firefox, ws://the-domain-name.tld/_blazor?id=XXX adresinde sunucuyla bağlantı kuramıyor.</span><span class="sxs-lookup"><span data-stu-id="4fa32-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="4fa32-182">Hata: ' WebSockets ' taşıması başlatılamadı: hata: aktarımda bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="4fa32-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="4fa32-183">Hata: ' LongPolling ' taşıması başlatılamadı: TypeError: this. Transport tanımsız</span><span class="sxs-lookup"><span data-stu-id="4fa32-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="4fa32-184">Hata: kullanılabilir aktarımlarla sunucuya bağlanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="4fa32-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="4fa32-185">WebSockets başarısız oldu</span><span class="sxs-lookup"><span data-stu-id="4fa32-185">WebSockets failed</span></span>
* <span data-ttu-id="4fa32-186">Hata: bağlantı ' bağlı ' durumunda değilse veri gönderilemez.</span><span class="sxs-lookup"><span data-stu-id="4fa32-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="4fa32-187">Daha fazla bilgi için [Apache belgelerine](https://httpd.apache.org/docs/current/mod/mod_proxy.html)bakın.</span><span class="sxs-lookup"><span data-stu-id="4fa32-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="4fa32-188">Ölçü ağı gecikmesi</span><span class="sxs-lookup"><span data-stu-id="4fa32-188">Measure network latency</span></span>

<span data-ttu-id="4fa32-189">Aşağıdaki örnekte gösterildiği gibi, [js birlikte çalışması](xref:blazor/call-javascript-from-dotnet) ağ gecikmesini ölçmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4fa32-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="4fa32-190">Makul bir kullanıcı arabirimi deneyimi için, 250ms veya daha az sayıda sürekli Kullanıcı arabirimi gecikme süresi önerilir.</span><span class="sxs-lookup"><span data-stu-id="4fa32-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
