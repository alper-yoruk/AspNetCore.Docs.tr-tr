---
title: Monitör ve hata ayıklama - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps çözümünün bir parçası olarak kodunuzu izleme ve hata ayıklama
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659504"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="ee76c-103">Monitör ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="ee76c-103">Monitor and debug</span></span>

<span data-ttu-id="ee76c-104">Uygulamayı dağıttıktan ve bir DevOps boru hattı inşa ettikten sonra, uygulamayı nasıl izleyip sorun gidereceklerini anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="ee76c-105">Bu bölümde, aşağıdaki görevleri tamamlayaceksiniz:</span><span class="sxs-lookup"><span data-stu-id="ee76c-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="ee76c-106">Azure portalında temel izleme ve sorun giderme verilerini bulma</span><span class="sxs-lookup"><span data-stu-id="ee76c-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ee76c-107">Azure Monitor'un tüm Azure hizmetlerinde ölçümlere nasıl daha yakından baktığını öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ee76c-108">Uygulama profil oluşturma için web uygulamasını Application Insights ile bağlayın</span><span class="sxs-lookup"><span data-stu-id="ee76c-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ee76c-109">Günlüğü açın ve günlükleri nereye indirebileceğinizi öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ee76c-110">Akış günlükleri gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="ee76c-110">Stream logs in real time</span></span>
* <span data-ttu-id="ee76c-111">Uyarıları nerede ayarladığınızı öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="ee76c-112">Azure App Service web uygulamalarını uzaktan hata ayıklama hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="ee76c-113">Temel izleme ve sorun giderme</span><span class="sxs-lookup"><span data-stu-id="ee76c-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="ee76c-114">App Service web uygulamaları gerçek zamanlı olarak kolayca izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="ee76c-115">Azure portalı, ölçümleri anlaşılması kolay grafiklerde ve grafiklerde işler.</span><span class="sxs-lookup"><span data-stu-id="ee76c-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="ee76c-116">Azure [portalını](https://portal.azure.com)açın ve ardından *\<mywebapp\> unique_number* Uygulama Hizmeti'ne gidin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="ee76c-117">**Genel Bakış** sekmesi, son ölçümleri görüntüleyen grafikler de dahil olmak üzere yararlı "bir bakışta" bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ee76c-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Genel bakış panelini gösteren ekran görüntüsü](./media/monitoring/overview.png)

    * <span data-ttu-id="ee76c-119">**Http 5xx**: Sunucu tarafı hataları, genellikle ASP.NET Core kodunda istisnalar sayısı.</span><span class="sxs-lookup"><span data-stu-id="ee76c-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="ee76c-120">**Data In**: Web uygulamanıza veri girişi geliyor.</span><span class="sxs-lookup"><span data-stu-id="ee76c-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="ee76c-121">**Veri Çıkış**: Web uygulamanızdan müşterilere veri çıkış.</span><span class="sxs-lookup"><span data-stu-id="ee76c-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="ee76c-122">**İstekler**: HTTP isteklerinin sayısı.</span><span class="sxs-lookup"><span data-stu-id="ee76c-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="ee76c-123">**Ortalama Yanıt Süresi**: Web uygulamasının HTTP isteklerini yanıtlaması için ortalama süre.</span><span class="sxs-lookup"><span data-stu-id="ee76c-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="ee76c-124">Sorun giderme ve en iyi duruma getirilmesi için çeşitli self servis araçları da bu sayfada bulunur.</span><span class="sxs-lookup"><span data-stu-id="ee76c-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Self servis araçlarını gösteren ekran görüntüsü](./media/monitoring/wizards.png)

    * <span data-ttu-id="ee76c-126">**Sorunları tanılamak ve çözmek** bir self servis sorun gidericidir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="ee76c-127">**Uygulama Öngörüleri,** performans ve uygulama davranışının profilini çıkarmak içindir ve daha sonra bu bölümde tartışılır.</span><span class="sxs-lookup"><span data-stu-id="ee76c-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="ee76c-128">**App Service Advisor,** uygulama deneyiminizi ayarlamak için önerilerde bulunur.</span><span class="sxs-lookup"><span data-stu-id="ee76c-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="ee76c-129">Gelişmiş izleme</span><span class="sxs-lookup"><span data-stu-id="ee76c-129">Advanced monitoring</span></span>

<span data-ttu-id="ee76c-130">[Azure Monitor,](/azure/monitoring-and-diagnostics/) Azure hizmetlerinde tüm ölçümleri izlemek ve uyarıları ayarlamak için merkezi bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="ee76c-131">Azure Monitor'da yöneticiler performansı ayrıntılı olarak izleyebilir ve eğilimleri belirleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="ee76c-132">Her Azure hizmeti Azure Monitor'a kendi [metrik kümesini](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) sunar.</span><span class="sxs-lookup"><span data-stu-id="ee76c-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="ee76c-133">Uygulama Öngörüleri ile Profil</span><span class="sxs-lookup"><span data-stu-id="ee76c-133">Profile with Application Insights</span></span>

<span data-ttu-id="ee76c-134">[Application Insights,](/azure/application-insights/app-insights-overview) web uygulamalarının performansını ve kararlılığını ve kullanıcıların bunları nasıl kullandığını analiz eden bir Azure hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="ee76c-135">Application Insights'tan alınan veriler Azure Monitor'dan daha geniş ve daha derindir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="ee76c-136">Veriler, geliştiricilere ve yöneticilere uygulamaları iyileştirmek için önemli bilgiler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="ee76c-137">Uygulama Öngörüleri, kod değişikliği olmadan Bir Azure Uygulama Hizmeti kaynağına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="ee76c-138">Azure [portalını](https://portal.azure.com)açın ve ardından *\<mywebapp\> unique_number* Uygulama Hizmeti'ne gidin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ee76c-139">Genel **Bakış** sekmesinden, **Uygulama Öngörüleri** döşemesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Uygulama Öngörüleri döşemesi](./media/monitoring/app-insights.png)

1. <span data-ttu-id="ee76c-141">Yeni **kaynak** radyo oluştur düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="ee76c-142">Varsayılan kaynak adını kullanın ve Application Insights kaynağının konumunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="ee76c-143">Konumun web uygulamanızla eşleşmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="ee76c-143">The location doesn't need to match that of your web app.</span></span>

    ![Uygulama Öngörüleri kurulumu](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="ee76c-145">**Runtime/Framework**için **ASP.NET Çekirdek'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="ee76c-146">Varsayılan ayarları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-146">Accept the default settings.</span></span>
1. <span data-ttu-id="ee76c-147">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-147">Select **OK**.</span></span> <span data-ttu-id="ee76c-148">Onaylamak istenirse Devam **et'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="ee76c-149">Kaynak oluşturulduktan sonra, doğrudan Application Insights sayfasına gitmek için Application Insights kaynağının adını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Yeni Uygulama Öngörüleri kaynağı hazır](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="ee76c-151">Uygulama kullanıldıkça veriler birikir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="ee76c-152">Bıçağı yeni verilerle yeniden yüklemek için **Yenile'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-152">Select **Refresh** to reload the blade with new data.</span></span>

![Uygulama Öngörüleri genel bakış sekmesi](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="ee76c-154">Application Insights, ek yapılandırma olmadan yararlı sunucu tarafı bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ee76c-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="ee76c-155">Application Insights'tan en yüksek değeri elde etmek için [Uygulama Öngörüleri SDK ile uygulamanızı](/azure/application-insights/app-insights-asp-net-core)enstrümanedin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="ee76c-156">Düzgün şekilde yapılandırıldığında, hizmet istemci tarafı performansı da dahil olmak üzere web sunucusu ve tarayıcı da uça izleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="ee76c-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="ee76c-157">Daha fazla bilgi için [Application Insights belgelerine](/azure/application-insights/app-insights-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="ee76c-158">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="ee76c-158">Logging</span></span>

<span data-ttu-id="ee76c-159">Azure Uygulama Hizmeti'nde web sunucusu ve uygulama günlükleri varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="ee76c-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="ee76c-160">Günlükleri aşağıdaki adımlarla etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="ee76c-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="ee76c-161">Azure [portalını](https://portal.azure.com)açın ve *mywebapp\<\> unique_number* Uygulama Hizmeti'ne gidin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ee76c-162">Soldaki menüde İzleme **bölümüne** gidin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="ee76c-163">**Tanılama günlüklerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-163">Select **Diagnostics logs**.</span></span>

    ![Tanılama günlükleri bağlantısı](./media/monitoring/logging.png)

1. <span data-ttu-id="ee76c-165">Uygulama **Günlüğe Kaydetme (Filesystem)** aç.</span><span class="sxs-lookup"><span data-stu-id="ee76c-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="ee76c-166">İstenirse, web uygulamasında uygulama günlüğü etkinleştirmek için uzantıları yüklemek için kutuyu tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="ee76c-167">**Web sunucusu günlüğe kaydetmeyi** **Dosya Sistemi'ne**ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="ee76c-168">Günler içinde **Bekletme Süresini** girin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="ee76c-169">Örneğin, 30.</span><span class="sxs-lookup"><span data-stu-id="ee76c-169">For example, 30.</span></span>
1. <span data-ttu-id="ee76c-170">**Kaydet**'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ee76c-170">Click **Save**.</span></span>

<span data-ttu-id="ee76c-171">ASP.NET Core ve web sunucusu (App Service) günlükleri web uygulaması için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ee76c-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="ee76c-172">Görüntülenen FTP/FTPS bilgileri kullanılarak indirilebilirler.</span><span class="sxs-lookup"><span data-stu-id="ee76c-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="ee76c-173">Parola, bu kılavuzda daha önce oluşturulan dağıtım kimlik bilgileriyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="ee76c-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="ee76c-174">Günlükler [PowerShell veya Azure CLI ile doğrudan yerel makinenize aktarılabilir.](/azure/app-service/web-sites-enable-diagnostic-log#download)</span><span class="sxs-lookup"><span data-stu-id="ee76c-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="ee76c-175">Günlükler Uygulama [Öngörüleri'nde](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)de görüntülenebilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="ee76c-176">Günlük akışı</span><span class="sxs-lookup"><span data-stu-id="ee76c-176">Log streaming</span></span>

<span data-ttu-id="ee76c-177">Uygulama ve web sunucusu günlükleri portal üzerinden gerçek zamanlı olarak aktarılabilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="ee76c-178">Azure [portalını](https://portal.azure.com)açın ve *mywebapp\<\> unique_number* Uygulama Hizmeti'ne gidin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ee76c-179">Soldaki menüde, **İzleme** bölümüne gidin ve **Log akışını**seçin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Günlük akışı bağlantısını gösteren ekran görüntüsü](./media/monitoring/log-stream.png)

<span data-ttu-id="ee76c-181">Günlükler, Bulut Kabuğu da dahil olmak üzere [Azure CLI veya Azure PowerShell üzerinden](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)de aktarılabilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="ee76c-182">Uyarılar</span><span class="sxs-lookup"><span data-stu-id="ee76c-182">Alerts</span></span>

<span data-ttu-id="ee76c-183">Azure Monitor ayrıca [ölçümlere,](/azure/monitoring-and-diagnostics/insights-alerts-portal) yönetim olaylarına ve diğer ölçütlere dayalı gerçek zamanlı uyarılar da sağlar.</span><span class="sxs-lookup"><span data-stu-id="ee76c-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="ee76c-184">*Not: Şu anda web uygulaması ölçümlerinde uyarı yalnızca Uyarılar (klasik) hizmetinde kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="ee76c-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="ee76c-185">[Uyarılar (klasik) hizmeti](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) Azure Monitor'da veya Uygulama Hizmeti ayarlarının **İzleme** bölümünde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Uyarılar (klasik) bağlantı](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="ee76c-187">Canlı hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="ee76c-187">Live debugging</span></span>

<span data-ttu-id="ee76c-188">Azure Uygulama Hizmeti, günlükler yeterli bilgi sağlamadığında [Visual Studio ile uzaktan debugged](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) olabilir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="ee76c-189">Ancak, uzaktan hata ayıklama, uygulamanın hata ayıklama sembolleriyle derlemil olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ee76c-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="ee76c-190">Son çare olarak dışında, üretimde hata ayıklama yapılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ee76c-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ee76c-191">Sonuç</span><span class="sxs-lookup"><span data-stu-id="ee76c-191">Conclusion</span></span>

<span data-ttu-id="ee76c-192">Bu bölümde, aşağıdaki görevleri tamamladınız:</span><span class="sxs-lookup"><span data-stu-id="ee76c-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="ee76c-193">Azure portalında temel izleme ve sorun giderme verilerini bulma</span><span class="sxs-lookup"><span data-stu-id="ee76c-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ee76c-194">Azure Monitor'un tüm Azure hizmetlerinde ölçümlere nasıl daha yakından baktığını öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ee76c-195">Uygulama profil oluşturma için web uygulamasını Application Insights ile bağlayın</span><span class="sxs-lookup"><span data-stu-id="ee76c-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ee76c-196">Günlüğü açın ve günlükleri nereye indirebileceğinizi öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ee76c-197">Akış günlükleri gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="ee76c-197">Stream logs in real time</span></span>
* <span data-ttu-id="ee76c-198">Uyarıları nerede ayarladığınızı öğrenin</span><span class="sxs-lookup"><span data-stu-id="ee76c-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="ee76c-199">Azure App Service web uygulamalarını uzaktan hata ayıklama hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="ee76c-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="ee76c-200">Ek okuma</span><span class="sxs-lookup"><span data-stu-id="ee76c-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="ee76c-201">Uygulama Öngörüleri ile Azure web uygulaması performansını izleyin</span><span class="sxs-lookup"><span data-stu-id="ee76c-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="ee76c-202">Azure Uygulama Hizmeti'nde web uygulamaları için tanılama günlemesini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="ee76c-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="ee76c-203">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="ee76c-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="ee76c-204">Azure hizmetleri için Azure Monitor'da klasik metrik uyarılar oluşturma - Azure portalı</span><span class="sxs-lookup"><span data-stu-id="ee76c-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
