---
title: ASP.NET Core ve Azure ile izleme ve hata ayıklama-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps çözümünün bir parçası olarak kodunuzu izleme ve hata ayıklama
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: 98d7c820b20f4f4c6b57f283ef718ce056dcdcae
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130502"
---
# <a name="monitor-and-debug"></a>İzleme ve hata ayıklama

Uygulamayı dağıttıysanız ve bir DevOps işlem hattı oluşturduysanız, uygulamanın nasıl izleneceğini ve giderileceğini anlamak önemlidir.

Bu bölümde, aşağıdaki görevleri tamamlayacaksınız:

* Azure portal temel izleme ve sorun giderme verilerini bulma
* Azure Izleyici 'nin tüm Azure hizmetleri genelinde ölçümler hakkında daha ayrıntılı bir bakış sağladığını öğrenin
* Uygulama profili oluşturma için Web uygulamasını Application Insights bağlama
* Günlüğe kaydetmeyi açın ve günlüklerin nereye indirileceği hakkında bilgi edinin
* Gerçek zamanlı akış günlükleri
* Uyarıları ayarlamayı öğrenin
* Web Apps Azure App Service uzaktan hata ayıklama hakkında bilgi edinin.

## <a name="basic-monitoring-and-troubleshooting"></a>Temel izleme ve sorun giderme

App Service Web Apps gerçek zamanlı olarak kolayca izlenir. Azure portal ölçümleri kolay anlaşılır grafikler ve grafiklerde işler.

1. [Azure Portal](https://portal.azure.com)açın ve ardından *MyWebApp \<unique_number\> * App Service gidin.

1. **Genel bakış** sekmesi, son ölçümleri görüntüleyen grafikler dahil yararlı "bir bakışta" bilgileri görüntüler.

    ![Genel bakış panelini gösteren ekran görüntüsü](./media/monitoring/overview.png)

    * **Http 5xx**: sunucu tarafı hatalarının sayısı, genellikle ASP.NET Core kodundaki özel durumlar.
    * **Veri girişi**: Web uygulamanıza gelen veri girişi.
    * **Giden veri**: Web uygulamanızdan istemcilere giden veri çıkışı.
    * **İstekler**: http isteklerinin sayısı.
    * **Ortalama yanıt süresi**: Web uygulamasının http isteklerine yanıt vermesi için geçen ortalama süre.

    Sorun giderme ve iyileştirme için birkaç self servis aracı da bu sayfada bulunur.

    ![Self Servis araçlarını gösteren ekran görüntüsü](./media/monitoring/wizards.png)

    * **Sorunları tanılama ve çözme** , bir self servis sorun gidericidir.
    * **Application Insights** , profil oluşturma performansı ve uygulama davranışına yöneliktir ve bu bölümün ilerleyen kısımlarında ele alınmıştır.
    * **App Service Danışmanı** , uygulama deneyiminizi ayarlama önerilerini sağlar.

## <a name="advanced-monitoring"></a>Gelişmiş izleme

[Azure izleyici](/azure/monitoring-and-diagnostics/) , tüm ölçümleri Izlemek ve Azure hizmetleri genelinde uyarı ayarlamak için merkezi bir hizmettir. Azure Izleyici 'de Yöneticiler, performansı döngüye alabilir ve eğilimleri tanımlayabilir. Her Azure hizmeti, Azure Izleyici 'ye kendi [ölçüm kümesini](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) sunar.

## <a name="profile-with-application-insights"></a>Application Insights profil

[Application Insights](/azure/application-insights/app-insights-overview) , Web uygulamalarının performansını ve kararlılığını ve kullanıcıların bunları nasıl kullandığını analiz eden bir Azure hizmetidir. Application Insights veriler Azure Izleyici 'den daha geniş ve daha ayrıntılı. Veriler geliştiricilere ve yöneticilere uygulamaları iyileştirmek için önemli bilgiler sağlayabilir. Application Insights, kod değişikliği olmadan bir Azure App Service kaynağına eklenebilir.

1. [Azure Portal](https://portal.azure.com)açın ve ardından *MyWebApp \<unique_number\> * App Service gidin.
1. **Genel bakış** sekmesinden **Application Insights** kutucuğuna tıklayın.

    ![Application Insights kutucuğu](./media/monitoring/app-insights.png)

1. **Yeni kaynak oluştur** radyo düğmesini seçin. Varsayılan kaynak adını kullanın ve Application Insights kaynak konumunu seçin. Konumun Web uygulamanızın ile eşleşmesi gerekmez.

    ![Application Insights kurulum](./media/monitoring/new-app-insights.png)

1. **Çalışma zamanı/çerçeve**için **ASP.NET Core**seçin. Varsayılan ayarları kabul edin.
1. **Tamam**’ı seçin. Onaylamanız istenirse **devam**' ı seçin.
1. Kaynak oluşturulduktan sonra, Application Insights sayfasına doğrudan gitmek için Application Insights kaynak adına tıklayın.

    ![Yeni Application Insights kaynak hazırlanıyor](./media/monitoring/new-app-insights-done.png)

Uygulama kullanıldığında, veriler birikir. Yeni verilerle dikey pencereyi yeniden yüklemek için **Yenile** ' yi seçin.

![Application Insights Genel Bakış sekmesi](./media/monitoring/app-insights-overview.png)

Application Insights, ek yapılandırma olmadan yararlı sunucu tarafı bilgileri sağlar. Application Insights en fazla değeri almak için [uygulamanızı APPLICATION INSIGHTS SDK ile işaretleyin](/azure/application-insights/app-insights-asp-net-core). Düzgün yapılandırıldığında, hizmet, istemci tarafı performansı dahil olmak üzere Web sunucusu ve tarayıcı genelinde uçtan uca izleme sağlar. Daha fazla bilgi için [Application Insights belgelerine](/azure/application-insights/app-insights-overview)bakın.

## <a name="logging"></a>Günlüğe Kaydetme

Web sunucusu ve uygulama günlükleri Azure App Service ' de varsayılan olarak devre dışıdır. Aşağıdaki adımlarla günlükleri etkinleştirin:

1. [Azure Portal](https://portal.azure.com)açın ve *MyWebApp \<unique_number\> * App Service gidin.
1. Soldaki menüde, **izleme** bölümüne gidin. **Tanılama günlükleri**' ni seçin.

    ![Tanılama günlükleri bağlantısı](./media/monitoring/logging.png)

1. **Uygulama günlüğünü açın (dosya sistemi)**. İstenirse, Web uygulamasında uygulama günlüğünü etkinleştirmek için uzantıları yüklemek üzere kutuya tıklayın.
1. **Web sunucusu günlüğünü** **dosya sistemine**ayarlayın.
1. **Saklama süresini** gün olarak girin. Örneğin, 30.
1. **Kaydet**’e tıklayın.

Web uygulaması için ASP.NET Core ve Web sunucusu (App Service) günlükleri oluşturulur. Bunlar, görüntülendikleri FTP/FTPS bilgileri kullanılarak indirilebilirler. Parola, bu kılavuzda daha önce oluşturulan dağıtım kimlik bilgileri ile aynıdır. Günlükler [doğrudan PowerShell veya Azure CLI ile yerel makinenize akışla](/azure/app-service/web-sites-enable-diagnostic-log#download)eklenebilir. Günlükler, Application Insights de [görüntülenebilir](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Günlük akışı

Uygulama ve Web sunucusu günlükleri, Portal aracılığıyla gerçek zamanlı olarak akışla eklenebilir.

1. [Azure Portal](https://portal.azure.com)açın ve *MyWebApp \<unique_number\> * App Service gidin.
1. Soldaki menüde, **izleme** bölümüne gidin ve **günlük akışı**' nı seçin.

    ![Günlük akışı bağlantısını gösteren ekran görüntüsü](./media/monitoring/log-stream.png)

Günlükler, Cloud Shell dahil olmak üzere [Azure CLI veya Azure PowerShell aracılığıyla da akışla](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)eklenebilir.

## <a name="alerts"></a>Uyarılar

Azure Izleyici Ayrıca ölçümler, yönetim olayları ve diğer ölçütlere göre [gerçek zamanlı uyarılar](/azure/monitoring-and-diagnostics/insights-alerts-portal) sağlar.

> *Note: Şu anda Web uygulaması ölçümlerinde uyarı verme yalnızca uyarılar (klasik) hizmetinde kullanılabilir.*

[Uyarılar (klasik) hizmeti](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) Azure izleyici 'de veya App Service ayarlarının **izleme** bölümünde bulunabilir.

![Uyarılar (klasik) bağlantısı](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Canlı hata ayıklama

Günlüklerde yeterli bilgi sağlamadığında, [Visual Studio ile Azure App Service uzaktan hata ayıklanabilir](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) . Ancak, uzaktan hata ayıklama uygulamanın hata ayıklama sembolleriyle derlenmesi gerektirir. Hata ayıklama, son çare dışında üretimde yapılmamalıdır.

## <a name="conclusion"></a>Sonuç

Bu bölümde, aşağıdaki görevleri tamamladınız:

* Azure portal temel izleme ve sorun giderme verilerini bulma
* Azure Izleyici 'nin tüm Azure hizmetleri genelinde ölçümler hakkında daha ayrıntılı bir bakış sağladığını öğrenin
* Uygulama profili oluşturma için Web uygulamasını Application Insights bağlama
* Günlüğe kaydetmeyi açın ve günlüklerin nereye indirileceği hakkında bilgi edinin
* Gerçek zamanlı akış günlükleri
* Uyarıları ayarlamayı öğrenin
* Web Apps Azure App Service uzaktan hata ayıklama hakkında bilgi edinin.

## <a name="additional-reading"></a>Ek okuma

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Application Insights ile Azure Web uygulaması performansını izleme](/azure/application-insights/app-insights-azure-web-apps)
* [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure hizmetleri için Azure Izleyici 'de klasik ölçüm uyarıları oluşturma-Azure portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
