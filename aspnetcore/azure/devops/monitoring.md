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
# <a name="monitor-and-debug"></a>Monitör ve hata ayıklama

Uygulamayı dağıttıktan ve bir DevOps boru hattı inşa ettikten sonra, uygulamayı nasıl izleyip sorun gidereceklerini anlamak önemlidir.

Bu bölümde, aşağıdaki görevleri tamamlayaceksiniz:

* Azure portalında temel izleme ve sorun giderme verilerini bulma
* Azure Monitor'un tüm Azure hizmetlerinde ölçümlere nasıl daha yakından baktığını öğrenin
* Uygulama profil oluşturma için web uygulamasını Application Insights ile bağlayın
* Günlüğü açın ve günlükleri nereye indirebileceğinizi öğrenin
* Akış günlükleri gerçek zamanlı
* Uyarıları nerede ayarladığınızı öğrenin
* Azure App Service web uygulamalarını uzaktan hata ayıklama hakkında bilgi edinin.

## <a name="basic-monitoring-and-troubleshooting"></a>Temel izleme ve sorun giderme

App Service web uygulamaları gerçek zamanlı olarak kolayca izlenebilir. Azure portalı, ölçümleri anlaşılması kolay grafiklerde ve grafiklerde işler.

1. Azure [portalını](https://portal.azure.com)açın ve ardından *\<mywebapp\> unique_number* Uygulama Hizmeti'ne gidin.

1. **Genel Bakış** sekmesi, son ölçümleri görüntüleyen grafikler de dahil olmak üzere yararlı "bir bakışta" bilgileri görüntüler.

    ![Genel bakış panelini gösteren ekran görüntüsü](./media/monitoring/overview.png)

    * **Http 5xx**: Sunucu tarafı hataları, genellikle ASP.NET Core kodunda istisnalar sayısı.
    * **Data In**: Web uygulamanıza veri girişi geliyor.
    * **Veri Çıkış**: Web uygulamanızdan müşterilere veri çıkış.
    * **İstekler**: HTTP isteklerinin sayısı.
    * **Ortalama Yanıt Süresi**: Web uygulamasının HTTP isteklerini yanıtlaması için ortalama süre.

    Sorun giderme ve en iyi duruma getirilmesi için çeşitli self servis araçları da bu sayfada bulunur.

    ![Self servis araçlarını gösteren ekran görüntüsü](./media/monitoring/wizards.png)

    * **Sorunları tanılamak ve çözmek** bir self servis sorun gidericidir.
    * **Uygulama Öngörüleri,** performans ve uygulama davranışının profilini çıkarmak içindir ve daha sonra bu bölümde tartışılır.
    * **App Service Advisor,** uygulama deneyiminizi ayarlamak için önerilerde bulunur.

## <a name="advanced-monitoring"></a>Gelişmiş izleme

[Azure Monitor,](/azure/monitoring-and-diagnostics/) Azure hizmetlerinde tüm ölçümleri izlemek ve uyarıları ayarlamak için merkezi bir hizmettir. Azure Monitor'da yöneticiler performansı ayrıntılı olarak izleyebilir ve eğilimleri belirleyebilir. Her Azure hizmeti Azure Monitor'a kendi [metrik kümesini](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) sunar.

## <a name="profile-with-application-insights"></a>Uygulama Öngörüleri ile Profil

[Application Insights,](/azure/application-insights/app-insights-overview) web uygulamalarının performansını ve kararlılığını ve kullanıcıların bunları nasıl kullandığını analiz eden bir Azure hizmetidir. Application Insights'tan alınan veriler Azure Monitor'dan daha geniş ve daha derindir. Veriler, geliştiricilere ve yöneticilere uygulamaları iyileştirmek için önemli bilgiler sağlayabilir. Uygulama Öngörüleri, kod değişikliği olmadan Bir Azure Uygulama Hizmeti kaynağına eklenebilir.

1. Azure [portalını](https://portal.azure.com)açın ve ardından *\<mywebapp\> unique_number* Uygulama Hizmeti'ne gidin.
1. Genel **Bakış** sekmesinden, **Uygulama Öngörüleri** döşemesini tıklatın.

    ![Uygulama Öngörüleri döşemesi](./media/monitoring/app-insights.png)

1. Yeni **kaynak** radyo oluştur düğmesini seçin. Varsayılan kaynak adını kullanın ve Application Insights kaynağının konumunu seçin. Konumun web uygulamanızla eşleşmesine gerek yoktur.

    ![Uygulama Öngörüleri kurulumu](./media/monitoring/new-app-insights.png)

1. **Runtime/Framework**için **ASP.NET Çekirdek'i**seçin. Varsayılan ayarları kabul edin.
1. **Tamam'ı**seçin. Onaylamak istenirse Devam **et'i**seçin.
1. Kaynak oluşturulduktan sonra, doğrudan Application Insights sayfasına gitmek için Application Insights kaynağının adını tıklatın.

    ![Yeni Uygulama Öngörüleri kaynağı hazır](./media/monitoring/new-app-insights-done.png)

Uygulama kullanıldıkça veriler birikir. Bıçağı yeni verilerle yeniden yüklemek için **Yenile'yi** seçin.

![Uygulama Öngörüleri genel bakış sekmesi](./media/monitoring/app-insights-overview.png)

Application Insights, ek yapılandırma olmadan yararlı sunucu tarafı bilgileri sağlar. Application Insights'tan en yüksek değeri elde etmek için [Uygulama Öngörüleri SDK ile uygulamanızı](/azure/application-insights/app-insights-asp-net-core)enstrümanedin. Düzgün şekilde yapılandırıldığında, hizmet istemci tarafı performansı da dahil olmak üzere web sunucusu ve tarayıcı da uça izleme sağlar. Daha fazla bilgi için [Application Insights belgelerine](/azure/application-insights/app-insights-overview)bakın.

## <a name="logging"></a>Günlüğe kaydetme

Azure Uygulama Hizmeti'nde web sunucusu ve uygulama günlükleri varsayılan olarak devre dışı bırakılır. Günlükleri aşağıdaki adımlarla etkinleştirin:

1. Azure [portalını](https://portal.azure.com)açın ve *mywebapp\<\> unique_number* Uygulama Hizmeti'ne gidin.
1. Soldaki menüde İzleme **bölümüne** gidin. **Tanılama günlüklerini**seçin.

    ![Tanılama günlükleri bağlantısı](./media/monitoring/logging.png)

1. Uygulama **Günlüğe Kaydetme (Filesystem)** aç. İstenirse, web uygulamasında uygulama günlüğü etkinleştirmek için uzantıları yüklemek için kutuyu tıklatın.
1. **Web sunucusu günlüğe kaydetmeyi** **Dosya Sistemi'ne**ayarlayın.
1. Günler içinde **Bekletme Süresini** girin. Örneğin, 30.
1. **Kaydet**'e tıklayın.

ASP.NET Core ve web sunucusu (App Service) günlükleri web uygulaması için oluşturulur. Görüntülenen FTP/FTPS bilgileri kullanılarak indirilebilirler. Parola, bu kılavuzda daha önce oluşturulan dağıtım kimlik bilgileriyle aynıdır. Günlükler [PowerShell veya Azure CLI ile doğrudan yerel makinenize aktarılabilir.](/azure/app-service/web-sites-enable-diagnostic-log#download) Günlükler Uygulama [Öngörüleri'nde](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)de görüntülenebilir.

## <a name="log-streaming"></a>Günlük akışı

Uygulama ve web sunucusu günlükleri portal üzerinden gerçek zamanlı olarak aktarılabilir.

1. Azure [portalını](https://portal.azure.com)açın ve *mywebapp\<\> unique_number* Uygulama Hizmeti'ne gidin.
1. Soldaki menüde, **İzleme** bölümüne gidin ve **Log akışını**seçin.

    ![Günlük akışı bağlantısını gösteren ekran görüntüsü](./media/monitoring/log-stream.png)

Günlükler, Bulut Kabuğu da dahil olmak üzere [Azure CLI veya Azure PowerShell üzerinden](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)de aktarılabilir.

## <a name="alerts"></a>Uyarılar

Azure Monitor ayrıca [ölçümlere,](/azure/monitoring-and-diagnostics/insights-alerts-portal) yönetim olaylarına ve diğer ölçütlere dayalı gerçek zamanlı uyarılar da sağlar.

> *Not: Şu anda web uygulaması ölçümlerinde uyarı yalnızca Uyarılar (klasik) hizmetinde kullanılabilir.*

[Uyarılar (klasik) hizmeti](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) Azure Monitor'da veya Uygulama Hizmeti ayarlarının **İzleme** bölümünde bulunabilir.

![Uyarılar (klasik) bağlantı](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Canlı hata ayıklama

Azure Uygulama Hizmeti, günlükler yeterli bilgi sağlamadığında [Visual Studio ile uzaktan debugged](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) olabilir. Ancak, uzaktan hata ayıklama, uygulamanın hata ayıklama sembolleriyle derlemil olmasını gerektirir. Son çare olarak dışında, üretimde hata ayıklama yapılmamalıdır.

## <a name="conclusion"></a>Sonuç

Bu bölümde, aşağıdaki görevleri tamamladınız:

* Azure portalında temel izleme ve sorun giderme verilerini bulma
* Azure Monitor'un tüm Azure hizmetlerinde ölçümlere nasıl daha yakından baktığını öğrenin
* Uygulama profil oluşturma için web uygulamasını Application Insights ile bağlayın
* Günlüğü açın ve günlükleri nereye indirebileceğinizi öğrenin
* Akış günlükleri gerçek zamanlı
* Uyarıları nerede ayarladığınızı öğrenin
* Azure App Service web uygulamalarını uzaktan hata ayıklama hakkında bilgi edinin.

## <a name="additional-reading"></a>Ek okuma

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Uygulama Öngörüleri ile Azure web uygulaması performansını izleyin](/azure/application-insights/app-insights-azure-web-apps)
* [Azure Uygulama Hizmeti'nde web uygulamaları için tanılama günlemesini etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure hizmetleri için Azure Monitor'da klasik metrik uyarılar oluşturma - Azure portalı](/azure/monitoring-and-diagnostics/insights-alerts-portal)
