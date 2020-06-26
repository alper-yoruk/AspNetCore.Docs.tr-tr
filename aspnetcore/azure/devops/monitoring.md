---
title: ASP.NET Core ve Azure ile izleme ve hata ayıklama-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps çözümünün bir parçası olarak kodunuzu izleme ve hata ayıklama
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: a94b1e0b5ce2a24cf22eb665c9bcd03c25ffa67f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400381"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="9dc2c-103">İzleme ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9dc2c-103">Monitor and debug</span></span>

<span data-ttu-id="9dc2c-104">Uygulamayı dağıttıysanız ve bir DevOps işlem hattı oluşturduysanız, uygulamanın nasıl izleneceğini ve giderileceğini anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="9dc2c-105">Bu bölümde, aşağıdaki görevleri tamamlayacaksınız:</span><span class="sxs-lookup"><span data-stu-id="9dc2c-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="9dc2c-106">Azure portal temel izleme ve sorun giderme verilerini bulma</span><span class="sxs-lookup"><span data-stu-id="9dc2c-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9dc2c-107">Azure Izleyici 'nin tüm Azure hizmetleri genelinde ölçümler hakkında daha ayrıntılı bir bakış sağladığını öğrenin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9dc2c-108">Uygulama profili oluşturma için Web uygulamasını Application Insights bağlama</span><span class="sxs-lookup"><span data-stu-id="9dc2c-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9dc2c-109">Günlüğe kaydetmeyi açın ve günlüklerin nereye indirileceği hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9dc2c-110">Gerçek zamanlı akış günlükleri</span><span class="sxs-lookup"><span data-stu-id="9dc2c-110">Stream logs in real time</span></span>
* <span data-ttu-id="9dc2c-111">Uyarıları ayarlamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="9dc2c-112">Web Apps Azure App Service uzaktan hata ayıklama hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="9dc2c-113">Temel izleme ve sorun giderme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="9dc2c-114">App Service Web Apps gerçek zamanlı olarak kolayca izlenir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="9dc2c-115">Azure portal ölçümleri kolay anlaşılır grafikler ve grafiklerde işler.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="9dc2c-116">[Azure Portal](https://portal.azure.com)açın ve ardından \*MyWebApp \<unique_number\> \* App Service gidin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="9dc2c-117">**Genel bakış** sekmesi, son ölçümleri görüntüleyen grafikler dahil yararlı "bir bakışta" bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Genel bakış panelini gösteren ekran görüntüsü](./media/monitoring/overview.png)

    * <span data-ttu-id="9dc2c-119">**Http 5xx**: sunucu tarafı hatalarının sayısı, genellikle ASP.NET Core kodundaki özel durumlar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="9dc2c-120">**Veri girişi**: Web uygulamanıza gelen veri girişi.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="9dc2c-121">**Giden veri**: Web uygulamanızdan istemcilere giden veri çıkışı.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="9dc2c-122">**İstekler**: http isteklerinin sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="9dc2c-123">**Ortalama yanıt süresi**: Web uygulamasının http isteklerine yanıt vermesi için geçen ortalama süre.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="9dc2c-124">Sorun giderme ve iyileştirme için birkaç self servis aracı da bu sayfada bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Self Servis araçlarını gösteren ekran görüntüsü](./media/monitoring/wizards.png)

    * <span data-ttu-id="9dc2c-126">**Sorunları tanılama ve çözme** , bir self servis sorun gidericidir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="9dc2c-127">**Application Insights** , profil oluşturma performansı ve uygulama davranışına yöneliktir ve bu bölümün ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="9dc2c-128">**App Service Danışmanı** , uygulama deneyiminizi ayarlama önerilerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="9dc2c-129">Gelişmiş izleme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-129">Advanced monitoring</span></span>

<span data-ttu-id="9dc2c-130">[Azure izleyici](/azure/monitoring-and-diagnostics/) , tüm ölçümleri Izlemek ve Azure hizmetleri genelinde uyarı ayarlamak için merkezi bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="9dc2c-131">Azure Izleyici 'de Yöneticiler, performansı döngüye alabilir ve eğilimleri tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="9dc2c-132">Her Azure hizmeti, Azure Izleyici 'ye kendi [ölçüm kümesini](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) sunar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="9dc2c-133">Application Insights profil</span><span class="sxs-lookup"><span data-stu-id="9dc2c-133">Profile with Application Insights</span></span>

<span data-ttu-id="9dc2c-134">[Application Insights](/azure/application-insights/app-insights-overview) , Web uygulamalarının performansını ve kararlılığını ve kullanıcıların bunları nasıl kullandığını analiz eden bir Azure hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="9dc2c-135">Application Insights veriler Azure Izleyici 'den daha geniş ve daha ayrıntılı.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="9dc2c-136">Veriler geliştiricilere ve yöneticilere uygulamaları iyileştirmek için önemli bilgiler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="9dc2c-137">Application Insights, kod değişikliği olmadan bir Azure App Service kaynağına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="9dc2c-138">[Azure Portal](https://portal.azure.com)açın ve ardından \*MyWebApp \<unique_number\> \* App Service gidin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9dc2c-139">**Genel bakış** sekmesinden **Application Insights** kutucuğuna tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights kutucuğu](./media/monitoring/app-insights.png)

1. <span data-ttu-id="9dc2c-141">**Yeni kaynak oluştur** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="9dc2c-142">Varsayılan kaynak adını kullanın ve Application Insights kaynak konumunu seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="9dc2c-143">Konumun Web uygulamanızın ile eşleşmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights kurulum](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="9dc2c-145">**Çalışma zamanı/çerçeve**için **ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="9dc2c-146">Varsayılan ayarları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-146">Accept the default settings.</span></span>
1. <span data-ttu-id="9dc2c-147">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-147">Select **OK**.</span></span> <span data-ttu-id="9dc2c-148">Onaylamanız istenirse **devam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="9dc2c-149">Kaynak oluşturulduktan sonra, Application Insights sayfasına doğrudan gitmek için Application Insights kaynak adına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Yeni Application Insights kaynak hazırlanıyor](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="9dc2c-151">Uygulama kullanıldığında, veriler birikir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="9dc2c-152">Yeni verilerle dikey pencereyi yeniden yüklemek için **Yenile** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights Genel Bakış sekmesi](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="9dc2c-154">Application Insights, ek yapılandırma olmadan yararlı sunucu tarafı bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="9dc2c-155">Application Insights en fazla değeri almak için [uygulamanızı APPLICATION INSIGHTS SDK ile işaretleyin](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="9dc2c-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="9dc2c-156">Düzgün yapılandırıldığında, hizmet, istemci tarafı performansı dahil olmak üzere Web sunucusu ve tarayıcı genelinde uçtan uca izleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="9dc2c-157">Daha fazla bilgi için [Application Insights belgelerine](/azure/application-insights/app-insights-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="9dc2c-158">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-158">Logging</span></span>

<span data-ttu-id="9dc2c-159">Web sunucusu ve uygulama günlükleri Azure App Service ' de varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="9dc2c-160">Aşağıdaki adımlarla günlükleri etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="9dc2c-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="9dc2c-161">[Azure Portal](https://portal.azure.com)açın ve \*MyWebApp \<unique_number\> \* App Service gidin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9dc2c-162">Soldaki menüde, **izleme** bölümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="9dc2c-163">**Tanılama günlükleri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-163">Select **Diagnostics logs**.</span></span>

    ![Tanılama günlükleri bağlantısı](./media/monitoring/logging.png)

1. <span data-ttu-id="9dc2c-165">**Uygulama günlüğünü açın (dosya sistemi)**.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="9dc2c-166">İstenirse, Web uygulamasında uygulama günlüğünü etkinleştirmek için uzantıları yüklemek üzere kutuya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="9dc2c-167">**Web sunucusu günlüğünü** **dosya sistemine**ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="9dc2c-168">**Saklama süresini** gün olarak girin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="9dc2c-169">Örneğin, 30.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-169">For example, 30.</span></span>
1. <span data-ttu-id="9dc2c-170">**Kaydet**’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-170">Click **Save**.</span></span>

<span data-ttu-id="9dc2c-171">Web uygulaması için ASP.NET Core ve Web sunucusu (App Service) günlükleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="9dc2c-172">Bunlar, görüntülendikleri FTP/FTPS bilgileri kullanılarak indirilebilirler.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="9dc2c-173">Parola, bu kılavuzda daha önce oluşturulan dağıtım kimlik bilgileri ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="9dc2c-174">Günlükler [doğrudan PowerShell veya Azure CLI ile yerel makinenize akışla](/azure/app-service/web-sites-enable-diagnostic-log#download)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="9dc2c-175">Günlükler, Application Insights de [görüntülenebilir](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="9dc2c-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="9dc2c-176">Günlük akışı</span><span class="sxs-lookup"><span data-stu-id="9dc2c-176">Log streaming</span></span>

<span data-ttu-id="9dc2c-177">Uygulama ve Web sunucusu günlükleri, Portal aracılığıyla gerçek zamanlı olarak akışla eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="9dc2c-178">[Azure Portal](https://portal.azure.com)açın ve \*MyWebApp \<unique_number\> \* App Service gidin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9dc2c-179">Soldaki menüde, **izleme** bölümüne gidin ve **günlük akışı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Günlük akışı bağlantısını gösteren ekran görüntüsü](./media/monitoring/log-stream.png)

<span data-ttu-id="9dc2c-181">Günlükler, Cloud Shell dahil olmak üzere [Azure CLI veya Azure PowerShell aracılığıyla da akışla](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="9dc2c-182">Uyarılar</span><span class="sxs-lookup"><span data-stu-id="9dc2c-182">Alerts</span></span>

<span data-ttu-id="9dc2c-183">Azure Izleyici Ayrıca ölçümler, yönetim olayları ve diğer ölçütlere göre [gerçek zamanlı uyarılar](/azure/monitoring-and-diagnostics/insights-alerts-portal) sağlar.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="9dc2c-184">*Note: Şu anda Web uygulaması ölçümlerinde uyarı verme yalnızca uyarılar (klasik) hizmetinde kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="9dc2c-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="9dc2c-185">[Uyarılar (klasik) hizmeti](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) Azure izleyici 'de veya App Service ayarlarının **izleme** bölümünde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Uyarılar (klasik) bağlantısı](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="9dc2c-187">Canlı hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="9dc2c-187">Live debugging</span></span>

<span data-ttu-id="9dc2c-188">Günlüklerde yeterli bilgi sağlamadığında, [Visual Studio ile Azure App Service uzaktan hata ayıklanabilir](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) .</span><span class="sxs-lookup"><span data-stu-id="9dc2c-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="9dc2c-189">Ancak, uzaktan hata ayıklama uygulamanın hata ayıklama sembolleriyle derlenmesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="9dc2c-190">Hata ayıklama, son çare dışında üretimde yapılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="9dc2c-191">Sonuç</span><span class="sxs-lookup"><span data-stu-id="9dc2c-191">Conclusion</span></span>

<span data-ttu-id="9dc2c-192">Bu bölümde, aşağıdaki görevleri tamamladınız:</span><span class="sxs-lookup"><span data-stu-id="9dc2c-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="9dc2c-193">Azure portal temel izleme ve sorun giderme verilerini bulma</span><span class="sxs-lookup"><span data-stu-id="9dc2c-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9dc2c-194">Azure Izleyici 'nin tüm Azure hizmetleri genelinde ölçümler hakkında daha ayrıntılı bir bakış sağladığını öğrenin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9dc2c-195">Uygulama profili oluşturma için Web uygulamasını Application Insights bağlama</span><span class="sxs-lookup"><span data-stu-id="9dc2c-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9dc2c-196">Günlüğe kaydetmeyi açın ve günlüklerin nereye indirileceği hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9dc2c-197">Gerçek zamanlı akış günlükleri</span><span class="sxs-lookup"><span data-stu-id="9dc2c-197">Stream logs in real time</span></span>
* <span data-ttu-id="9dc2c-198">Uyarıları ayarlamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="9dc2c-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="9dc2c-199">Web Apps Azure App Service uzaktan hata ayıklama hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="9dc2c-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="9dc2c-200">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="9dc2c-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="9dc2c-201">Application Insights ile Azure Web uygulaması performansını izleme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="9dc2c-202">Azure App Service Web Apps için tanılama günlüğünü etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="9dc2c-203">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="9dc2c-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="9dc2c-204">Azure hizmetleri için Azure Izleyici 'de klasik ölçüm uyarıları oluşturma-Azure portal</span><span class="sxs-lookup"><span data-stu-id="9dc2c-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
