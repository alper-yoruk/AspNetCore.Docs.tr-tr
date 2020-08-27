---
title: Visual Studio ile Azure API Management bir ASP.NET Core Web API 'SI yayımlama
author: codemillmatt
description: ASP.NET Core Web API 'sini Visual Studio kullanarak Azure API Management yayımlamayı öğrenin.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945760"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Visual Studio ile Azure API Management bir ASP.NET Core Web API 'SI yayımlama

[Matt Soucoup](https://twitter.com/codemillmatt) tarafından

## <a name="set-up"></a>Kurulum

- Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.
- Henüz yapmadıysanız [Yeni bir Azure API Management örneği oluşturun](/azure/api-management/get-started-create-service-instance) .
- [Bir Web API uygulaması projesi oluşturun](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Uygulamayı yapılandırma

ASP.NET Core Web API 'sine Swagger tanımlarını eklemek, Azure API Management uygulamasının API tanımlarını okumasına olanak tanır. Aşağıdaki adımları tamamlayın.

### <a name="add-swagger"></a>Swagger ekleme

1. [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet paketini ASP.NET Core Web API projesine ekleyin:

    ![NuGet iletişim kutusunu yapılandırmak için ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. `Startup.ConfigureServices` yöntemine aşağıdaki satırı ekleyin:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. `Startup.Configure` yöntemine aşağıdaki satırı ekleyin:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>API yönlendirmesini değiştirme

Ardından, için gereken URL yapısını, öğesine erişmek için değiştirirsiniz `Get` `WeatherForecastController` . Aşağıdaki adımları tamamlayın:

1. *WeatherForecastController.cs* dosyasını açın.
1. `[Route("[controller]")]`Sınıf düzeyi özniteliğini silin. Sınıf tanımı aşağıdaki gibi görünür:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Eyleme bir `[Route("/")]` öznitelik ekleyin `Get()` . İşlev tanımı aşağıdakine benzer şekilde görünür:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Web API 'sini Azure App Service için yayımlayın

ASP.NET Core Web API 'sini Azure API Management yayımlamak için aşağıdaki adımları izleyin:

1. Azure App Service için API uygulamasını yayımlayın.
1. Azure API Management hizmet örneğine boş bir API ekleyin.
1. ASP.NET Core Web API uygulamasını Azure API Management hizmeti örneğine yayımlayın.

### <a name="publish-the-api-app-to-azure-app-service"></a>API uygulamasını Azure App Service yayımlayın

ASP.NET Core Web API 'sini Azure API Management yayımlamak için aşağıdaki adımları izleyin:

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **Yayımla**' yı seçin:

    ![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. **Yayımla** Iletişim kutusunda **Azure** ' ı seçin ve **İleri** düğmesini seçin:

    ![Yayımla iletişim kutusu](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. **Azure App Service (Windows)** öğesini seçin ve **İleri** düğmesini seçin:

    ![Yayımla iletişim kutusu: App Service seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. **Yeni Azure App Service oluştur**' u seçin.

    ![Yayımla iletişim kutusu: Azure hizmet örneği seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    **App Service oluştur** iletişim kutusu görüntülenir. **Uygulama adı**, **kaynak grubu**ve **App Service planı** giriş alanları doldurulur. Bu adları koruyabilir veya değiştirebilirsiniz.
1. **Oluştur** düğmesini seçin.

    ![App Service Oluştur iletişim kutusu](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Yayımla** iletişim kutusu odağı yeniden alır. Oluşturulan örnek otomatik olarak seçilir.

![Yayımla iletişim kutusu: App Service örneği seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

Bu noktada, Azure API Management hizmetine bir API eklemeniz gerekir. Aşağıdaki görevleri tamamladıktan sonra Visual Studio 'Yu açık bırakın.

### <a name="add-an-api-to-azure-api-management"></a>Azure API Management bir API ekleme

1. Daha önce Azure portal oluşturulan API Management hizmet örneğini açın. **API** dikey penceresini seçin:

    ![API Management hizmet örneğinden seçilen API dikey penceresi](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. **Yenı API Ekle** PANELINDEN **boş API** kutucuğunu seçin:

    ![Boş API kutucuğunun vurgulandığını gösteren ekran](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Görüntülenen **boş API oluştur** iletişim kutusuna aşağıdaki değerleri girin:    

    - **Görünen ad**: *hava tahminleri*
    - **Ad**: *hava tahminleri*
    - **API URL 'si soneki**: *v1*
    - **Web hizmeti URL 'si** alanını boş bırakın.

1. **Oluştur** düğmesini seçin.

    ![Tamamlanan boş API oluştur iletişim kutusunun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Boş API oluşturulur.

![API Management portalında boş bir API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>ASP.NET Core Web API 'sini Azure 'da yayımlayın API Management

1. Visual Studio’ya geri dönün. **Yayınlama** iletişim kutusu hala, daha önce kaldığınız yerden açık olmalıdır.
1. Vurgulanmış olan yeni yayımlanmış Azure App Service seçin.
1. **İleri** düğmesini seçin.

    ![App Service seçiliyken Yayımla iletişim kutusunun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. İletişim kutusunda artık daha önce oluşturulan Azure API Management hizmeti görüntülenir. Oluşturduğunuz boş API 'yi görmek için onu ve *API 'leri* klasörünü genişletin.
1. Boş API 'nin adını seçin ve **son** düğmesini seçin.

    ![Yeni oluşturulan Azure API Management boş API 'nin Yayımla iletişim kutusunda seçilen ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. İletişim kutusu kapanır ve yayımlama hakkında bilgi içeren bir Özet ekranı görüntülenir. **Yayımla** düğmesini seçin.

    ![Yayımlama profili görüntülenirken Visual Studio 'nun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Web API 'SI hem Azure App Service hem de Azure API Management yayımlanacak. Yeni bir tarayıcı penceresi görünür ve Azure App Service ' de çalışan API 'YI gösterir. Bu pencereyi kapatabilirsiniz.

1. Azure portal Azure API Management örneğine geri dönün.
1. Tarayıcı penceresini yenileyin.
1. Önceki adımlarda oluşturduğunuz boş API 'YI seçin. Artık doldurulmuştur ve araştırmanızı sağlayabilirsiniz.

    ![Azure API Management doldurulmuş API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Yayınlanan API adını yapılandırın

API 'nin adının, bu adı verdiklerinize göre farklı olduğuna dikkat edin. Yayınlanan API 'nin adı, *dalgalı API*; Ancak, onu oluştururken bu *hava tahminlerini* adlandırmış olursunuz. Adı onarmak için aşağıdaki adımları izleyin:

![Farklı adlara sahip doldurulmuş API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Yönteminden aşağıdaki satırı silin `Startup.ConfigureServices` :
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. `Startup.ConfigureServices` yöntemine aşağıdaki kodu ekleyin:
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. Yeni oluşturulan yayımlama profilini açın. *Özellikler/PublishProfiles* klasöründeki **Çözüm Gezgini** bulunabilir.

    ![Yayımlama profili dosyasının konumunu gösteren ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. `<OpenAPIDocumentName>`Öğenin değerini `v1` olarak değiştirin `WeatherForecasts` .

    ![Yayımlama profili için gereken değişikliklerin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. ASP.NET Core Web API 'sini yeniden yayımlayın ve Azure portal Azure API Management örneğini açın.
1. Tarayıcınızda sayfayı yenileyin. Artık API 'nin adını doğru görürsünüz.

    ![Portalda tamamlanan API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Web API 'sinin çalıştığını doğrulama

Azure API Management dağıtılan ASP.NET Core Web API 'sini aşağıdaki adımlarla Azure portal test edebilirsiniz:

1. **Test** sekmesini açın.
1. **/** Ya da **Al** işlemini seçin.
1. **Gönder**’i seçin.

    ![Testten önce portalın ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Başarılı bir yanıt aşağıdaki gibi görünür:

![API Management başarılı bir yanıtın ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Temizleme

Uygulamayı test etmeyi bitirdiğinizde [Azure Portal](https://portal.azure.com/) gidin ve uygulamayı silin.

1. **Kaynak grupları**' nı ve ardından oluşturduğunuz kaynak grubunu seçin.

    ![Azure portalı: kenar çubuğu menüsündeki kaynak grupları](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. **Kaynak grupları** sayfasında **Sil**' i seçin.

    ![Azure portalı: kaynak grupları sayfası](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Kaynak grubunun adını girip **Sil**' i seçin. Uygulamanız ve bu öğreticide oluşturulan diğer tüm kaynaklar artık Azure 'dan silinir.

## <a name="next-steps"></a>Sonraki adımlar

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure API Management](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
