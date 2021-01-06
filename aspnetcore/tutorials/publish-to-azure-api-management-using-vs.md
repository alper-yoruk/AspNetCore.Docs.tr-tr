---
title: Visual Studio ile Azure API Management bir ASP.NET Core Web API 'SI yayımlama
author: codemillmatt
description: ASP.NET Core Web API 'sini Visual Studio kullanarak Azure API Management yayımlamayı öğrenin.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96332245"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="a24ef-103">Visual Studio ile Azure API Management bir ASP.NET Core Web API 'SI yayımlama</span><span class="sxs-lookup"><span data-stu-id="a24ef-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="a24ef-104">[Matt Soucoup](https://twitter.com/codemillmatt) tarafından</span><span class="sxs-lookup"><span data-stu-id="a24ef-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="a24ef-105">Kurulum</span><span class="sxs-lookup"><span data-stu-id="a24ef-105">Set up</span></span>

- <span data-ttu-id="a24ef-106">Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="a24ef-107">Henüz yapmadıysanız [Yeni bir Azure API Management örneği oluşturun](/azure/api-management/get-started-create-service-instance) .</span><span class="sxs-lookup"><span data-stu-id="a24ef-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="a24ef-108">[Bir Web API uygulaması projesi oluşturun](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="a24ef-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="a24ef-109">Uygulamayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a24ef-109">Configure the app</span></span>

<span data-ttu-id="a24ef-110">ASP.NET Core Web API 'sine Swagger tanımlarını eklemek, Azure API Management uygulamasının API tanımlarını okumasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="a24ef-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="a24ef-111">Aşağıdaki adımları tamamlayın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="a24ef-112">Swagger ekleme</span><span class="sxs-lookup"><span data-stu-id="a24ef-112">Add Swagger</span></span>

1. <span data-ttu-id="a24ef-113">[Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet paketini ASP.NET Core Web API projesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![NuGet iletişim kutusunu yapılandırmak için ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="a24ef-115">`Startup.ConfigureServices` yöntemine aşağıdaki satırı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="a24ef-116">`Startup.Configure` yöntemine aşağıdaki satırı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="a24ef-117">API yönlendirmesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="a24ef-117">Change the API routing</span></span>

<span data-ttu-id="a24ef-118">Ardından, için gereken URL yapısını, öğesine erişmek için değiştirirsiniz `Get` `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="a24ef-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="a24ef-119">Aşağıdaki adımları tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="a24ef-119">Complete the following steps:</span></span>

1. <span data-ttu-id="a24ef-120">*WeatherForecastController.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="a24ef-121">`[Route("[controller]")]`Sınıf düzeyi özniteliğini silin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="a24ef-122">Sınıf tanımı aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a24ef-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="a24ef-123">Eyleme bir `[Route("/")]` öznitelik ekleyin `Get()` .</span><span class="sxs-lookup"><span data-stu-id="a24ef-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="a24ef-124">İşlev tanımı aşağıdakine benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="a24ef-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="a24ef-125">Web API 'sini Azure App Service için yayımlayın</span><span class="sxs-lookup"><span data-stu-id="a24ef-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="a24ef-126">ASP.NET Core Web API 'sini Azure API Management yayımlamak için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="a24ef-127">Azure App Service için API uygulamasını yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="a24ef-128">Azure API Management hizmet örneğine boş bir API ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="a24ef-129">ASP.NET Core Web API uygulamasını Azure API Management hizmeti örneğine yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="a24ef-130">API uygulamasını Azure App Service yayımlayın</span><span class="sxs-lookup"><span data-stu-id="a24ef-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="a24ef-131">ASP.NET Core Web API 'sini Azure API Management yayımlamak için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="a24ef-132">**Çözüm Gezgini**, projeye sağ tıklayın ve **Yayımla**' yı seçin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="a24ef-134">**Yayımla** Iletişim kutusunda **Azure** ' ı seçin ve **İleri** düğmesini seçin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Yayımla iletişim kutusu](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="a24ef-136">**Azure App Service (Windows)** öğesini seçin ve **İleri** düğmesini seçin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Yayımla iletişim kutusu: App Service seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="a24ef-138">**Yeni Azure App Service oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-138">Select **Create a new Azure App Service**.</span></span>

    ![Yayımla iletişim kutusu: Azure hizmet örneği seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="a24ef-140">**App Service oluştur** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="a24ef-141">**Uygulama adı**, **kaynak grubu** ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="a24ef-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a24ef-142">Bu adları koruyabilir veya değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a24ef-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="a24ef-143">**Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-143">Select the **Create** button.</span></span>

    ![App Service Oluştur iletişim kutusu](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="a24ef-145">Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Yayımla** iletişim kutusu odağı yeniden alır.</span><span class="sxs-lookup"><span data-stu-id="a24ef-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="a24ef-146">Oluşturulan örnek otomatik olarak seçilir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-146">The instance that was created is automatically selected.</span></span>

![Yayımla iletişim kutusu: App Service örneği seçin](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="a24ef-148">Bu noktada, Azure API Management hizmetine bir API eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="a24ef-149">Aşağıdaki görevleri tamamladıktan sonra Visual Studio 'Yu açık bırakın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="a24ef-150">Azure API Management bir API ekleme</span><span class="sxs-lookup"><span data-stu-id="a24ef-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="a24ef-151">Daha önce Azure portal oluşturulan API Management hizmet örneğini açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="a24ef-152">**API** dikey penceresini seçin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-152">Select the **APIs** blade:</span></span>

  ![API Management hizmet örneğinden seçilen API dikey penceresi](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="a24ef-154">**Echo API 'sinin** yanındaki 3 noktayı seçin ve ardından kaldırmak için açılır menüden **Sil** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![API Management hizmet örneğinden yankı API 'SI siliniyor](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="a24ef-156">**Yenı API Ekle** PANELINDEN **boş API** kutucuğunu seçin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Boş API kutucuğunun vurgulandığını gösteren ekran](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="a24ef-158">Görüntülenen **boş API oluştur** iletişim kutusuna aşağıdaki değerleri girin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="a24ef-159">**Görünen ad**: *hava tahminleri*</span><span class="sxs-lookup"><span data-stu-id="a24ef-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="a24ef-160">**Ad**: *hava tahminleri*</span><span class="sxs-lookup"><span data-stu-id="a24ef-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="a24ef-161">**API URL 'si soneki**: *v1*</span><span class="sxs-lookup"><span data-stu-id="a24ef-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="a24ef-162">**Web hizmeti URL 'si** alanını boş bırakın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="a24ef-163">**Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-163">Select the **Create** button.</span></span>

    ![Tamamlanan boş API oluştur iletişim kutusunun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="a24ef-165">Boş API oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a24ef-165">The blank API is created.</span></span>

![API Management portalında boş bir API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="a24ef-167">ASP.NET Core Web API 'sini Azure 'da yayımlayın API Management</span><span class="sxs-lookup"><span data-stu-id="a24ef-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="a24ef-168">Visual Studio’ya geri dönün.</span><span class="sxs-lookup"><span data-stu-id="a24ef-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="a24ef-169">**Yayınlama** iletişim kutusu hala, daha önce kaldığınız yerden açık olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a24ef-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="a24ef-170">Vurgulanmış olan yeni yayımlanmış Azure App Service seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="a24ef-171">**İleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-171">Select the **Next** button.</span></span>

    ![App Service seçiliyken Yayımla iletişim kutusunun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="a24ef-173">İletişim kutusunda artık daha önce oluşturulan Azure API Management hizmeti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="a24ef-174">Oluşturduğunuz boş API 'yi görmek için onu ve *API 'leri* klasörünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="a24ef-175">Boş API 'nin adını seçin ve **son** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Yeni oluşturulan Azure API Management boş API 'nin Yayımla iletişim kutusunda seçilen ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="a24ef-177">İletişim kutusu kapanır ve yayımlama hakkında bilgi içeren bir Özet ekranı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="a24ef-178">**Yayımla** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-178">Select the **Publish** button.</span></span>

    ![Yayımlama profili görüntülenirken Visual Studio 'nun ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="a24ef-180">Web API 'SI hem Azure App Service hem de Azure API Management yayımlanacak.</span><span class="sxs-lookup"><span data-stu-id="a24ef-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="a24ef-181">Yeni bir tarayıcı penceresi görünür ve Azure App Service ' de çalışan API 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="a24ef-182">Bu pencereyi kapatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a24ef-182">You can close that window.</span></span>

1. <span data-ttu-id="a24ef-183">Azure portal Azure API Management örneğine geri dönün.</span><span class="sxs-lookup"><span data-stu-id="a24ef-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="a24ef-184">Tarayıcı penceresini yenileyin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="a24ef-185">Önceki adımlarda oluşturduğunuz boş API 'YI seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="a24ef-186">Artık doldurulmuştur ve araştırmanızı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a24ef-186">It's now populated and you can explore around.</span></span>

    ![Azure API Management doldurulmuş API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="a24ef-188">Yayınlanan API adını yapılandırın</span><span class="sxs-lookup"><span data-stu-id="a24ef-188">Configure the published API name</span></span>

<span data-ttu-id="a24ef-189">API 'nin adının, bu adı verdiklerinize göre farklı olduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="a24ef-190">Yayınlanan API 'nin adı, *dalgalı API*; Ancak, onu oluştururken bu *hava tahminlerini* adlandırmış olursunuz.</span><span class="sxs-lookup"><span data-stu-id="a24ef-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="a24ef-191">Adı onarmak için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-191">Complete the following steps to fix the name:</span></span>

![Farklı adlara sahip doldurulmuş API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="a24ef-193">Yönteminden aşağıdaki satırı silin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a24ef-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="a24ef-194">`Startup.ConfigureServices` yöntemine aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a24ef-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
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

1. <span data-ttu-id="a24ef-195">Yeni oluşturulan yayımlama profilini açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-195">Open the newly created publish profile.</span></span> <span data-ttu-id="a24ef-196">*Özellikler/PublishProfiles* klasöründeki **Çözüm Gezgini** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Yayımlama profili dosyasının konumunu gösteren ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="a24ef-198">`<OpenAPIDocumentName>`Öğenin değerini `v1` olarak değiştirin `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="a24ef-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![Yayımlama profili için gereken değişikliklerin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="a24ef-200">ASP.NET Core Web API 'sini yeniden yayımlayın ve Azure portal Azure API Management örneğini açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="a24ef-201">Tarayıcınızda sayfayı yenileyin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-201">Refresh the page in your browser.</span></span> <span data-ttu-id="a24ef-202">Artık API 'nin adını doğru görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a24ef-202">You'll see the name of the API is now correct.</span></span>

    ![Portalda tamamlanan API 'nin ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="a24ef-204">Web API 'sinin çalıştığını doğrulama</span><span class="sxs-lookup"><span data-stu-id="a24ef-204">Verify the web API is working</span></span>

<span data-ttu-id="a24ef-205">Azure API Management dağıtılan ASP.NET Core Web API 'sini aşağıdaki adımlarla Azure portal test edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a24ef-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="a24ef-206">**Test** sekmesini açın.</span><span class="sxs-lookup"><span data-stu-id="a24ef-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="a24ef-207">**/** Ya da **Al** işlemini seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="a24ef-208">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-208">Select **Send**.</span></span>

    ![Testten önce portalın ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="a24ef-210">Başarılı bir yanıt aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a24ef-210">A successful response will look like the following:</span></span>

![API Management başarılı bir yanıtın ekran görüntüsü](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="a24ef-212">Temizleme</span><span class="sxs-lookup"><span data-stu-id="a24ef-212">Clean up</span></span>

<span data-ttu-id="a24ef-213">Uygulamayı test etmeyi bitirdiğinizde [Azure Portal](https://portal.azure.com/) gidin ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="a24ef-214">**Kaynak grupları**' nı ve ardından oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure portalı: kenar çubuğu menüsündeki kaynak grupları](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="a24ef-216">**Kaynak grupları** sayfasında **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure portalı: kaynak grupları sayfası](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="a24ef-218">Kaynak grubunun adını girip **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a24ef-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="a24ef-219">Uygulamanız ve bu öğreticide oluşturulan diğer tüm kaynaklar artık Azure 'dan silinir.</span><span class="sxs-lookup"><span data-stu-id="a24ef-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a24ef-220">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="a24ef-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="a24ef-221">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a24ef-221">Additional resources</span></span>

- [<span data-ttu-id="a24ef-222">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="a24ef-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="a24ef-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a24ef-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
