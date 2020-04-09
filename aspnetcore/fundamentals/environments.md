---
title: ASP.NET Core'da birden çok ortam kullanma
author: rick-anderson
description: ASP.NET Core uygulamalarında birden fazla ortamda uygulama davranışını nasıl denetleriz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656221"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="0ac60-103">ASP.NET Core'da birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="0ac60-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0ac60-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0ac60-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0ac60-105">ASP.NET Core, bir ortam değişkenini kullanarak uygulama davranışını çalışma zamanı ortamına göre yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="0ac60-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ac60-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="0ac60-107">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="0ac60-107">Environments</span></span>

<span data-ttu-id="0ac60-108">ASP.NET Core uygulama `ASPNETCORE_ENVIRONMENT` başlangıç çevre değişkeni okur ve [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName)değeri depolar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="0ac60-109">`ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak üç değer çerçeve tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="0ac60-110"><xref:Microsoft.Extensions.Hosting.Environments.Production>(varsayılan)</span><span class="sxs-lookup"><span data-stu-id="0ac60-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="0ac60-111">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0ac60-111">The preceding code:</span></span>

* <span data-ttu-id="0ac60-112">Çağırır [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) olarak ayarlandığında `ASPNETCORE_ENVIRONMENT` `Development`.</span><span class="sxs-lookup"><span data-stu-id="0ac60-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="0ac60-113">Değeri `ASPNETCORE_ENVIRONMENT` aşağıdakilerden biri ayarlandığında [UseExceptionHandler'ı](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) çağırır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="0ac60-114">[Çevre Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` öğeye biçimlendirme eklemek veya hariç tutmak için aşağıdakileri kullanır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="0ac60-115">Windows ve macOS'ta ortam değişkenleri ve değerleri büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="0ac60-116">Linux ortamı değişkenleri ve değerleri varsayılan olarak **büyük/küçük harf duyarlıdır.**</span><span class="sxs-lookup"><span data-stu-id="0ac60-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="0ac60-117">Geliştirme</span><span class="sxs-lookup"><span data-stu-id="0ac60-117">Development</span></span>

<span data-ttu-id="0ac60-118">Geliştirme ortamı, üretimde açığa çıkmaması gereken özellikleri etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="0ac60-119">Örneğin, ASP.NET Çekirdek şablonları geliştirme ortamında [Geliştirici Özel Durum Sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="0ac60-120">Yerel makine geliştirme ortamı projenin *Properties\launchSettings.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="0ac60-121">*launchSettings.json'da* ayarlanan ortam değerleri sistem ortamında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="0ac60-122">Aşağıdaki JSON *bir launchSettings.json* dosyasından üç profilleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="0ac60-123">`applicationUrl` *launchSettings.json'daki* özellik, sunucu URL'lerinin bir listesini belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="0ac60-124">Listedeki URL'ler arasında bir yarı nokta kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="0ac60-125">[Uygulama dotnet çalıştır](/dotnet/core/tools/dotnet-run)ı ile başlatıldığında, `"commandName": "Project"` ilk profil kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="0ac60-126">Değer başlatmak `commandName` için web sunucusu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="0ac60-127">`commandName`aşağıdakilerden herhangi biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="0ac60-128">`Project`(Hangi Kerkenez başlattı)</span><span class="sxs-lookup"><span data-stu-id="0ac60-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="0ac60-129">Bir uygulama [dotnet çalıştırılan](/dotnet/core/tools/dotnet-run)başlatıldığında:</span><span class="sxs-lookup"><span data-stu-id="0ac60-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="0ac60-130">*launchSettings.json* varsa okunur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="0ac60-131">`environmentVariables`*launchSettings.json'daki* ayarlar ortam değişkenlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="0ac60-132">Barındırma ortamı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="0ac60-133">Aşağıdaki [çıktı, dotnet çalıştırıile](/dotnet/core/tools/dotnet-run)başlayan bir uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="0ac60-134">Visual Studio proje özellikleri **Hata ayıklama** sekmesi *launchSettings.json* dosyasını düzenlemek için bir GUI sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proje Özellikleri Ayar Ortamı değişkenleri](environments/_static/project-properties-debug.png)

<span data-ttu-id="0ac60-136">Proje profillerinde yapılan değişiklikler, web sunucusu yeniden başlatılına kadar etkili olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="0ac60-137">Kerkenez, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="0ac60-138">*launchSettings.json* sırları saklamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="0ac60-139">[Gizli Yönetici aracı](xref:security/app-secrets) yerel gelişim için sırları depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="0ac60-140">Visual [Studio Code](https://code.visualstudio.com/)kullanırken ortam değişkenleri *.vscode/launch.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="0ac60-141">Aşağıdaki örnek, ortamı `Development`şu şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="0ac60-142">Projedeki *bir .vscode/launch.json* dosyası, uygulama `dotnet run` nın *özellikleri/launchSettings.json*ile aynı şekilde başlatıldığında okunmaz.</span><span class="sxs-lookup"><span data-stu-id="0ac60-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="0ac60-143">*LaunchSettings.json* dosyası olmayan geliştirme aşamasında bir uygulama başlatırken, ortamı bir ortam değişkeni veya `dotnet run` komut satırı bağımsız değişkeniyle komuta ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="0ac60-144">Üretim</span><span class="sxs-lookup"><span data-stu-id="0ac60-144">Production</span></span>

<span data-ttu-id="0ac60-145">Üretim ortamı, güvenliği, performansı ve uygulama sağlamlığını en üst düzeye çıkaracak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="0ac60-146">Geliştirmeden farklı olan bazı yaygın ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="0ac60-147">Önbelleğe alma.</span><span class="sxs-lookup"><span data-stu-id="0ac60-147">Caching.</span></span>
* <span data-ttu-id="0ac60-148">İstemci tarafı kaynakları bir CDN'den paketlenir, minified edilir ve potansiyel olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="0ac60-149">Tanılama hata sayfaları devre dışı.</span><span class="sxs-lookup"><span data-stu-id="0ac60-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="0ac60-150">Dost hata sayfaları etkin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="0ac60-151">Üretim günlüğü ve izleme etkin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="0ac60-152">Örneğin, [Uygulama Öngörüleri.](/azure/application-insights/app-insights-asp-net-core)</span><span class="sxs-lookup"><span data-stu-id="0ac60-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0ac60-153">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ac60-153">Set the environment</span></span>

<span data-ttu-id="0ac60-154">Bir ortam değişkeni veya platform ayarı ile sınama için belirli bir ortam ayarlamak genellikle yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="0ac60-155">Ortam ayarlanmıyorsa, varsayılan olarak `Production`hata ayıklama özelliklerinin çoğunu devre dışı bırakmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="0ac60-156">Ortamı ayarlama yöntemi işletim sistemine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="0ac60-157">Ana bilgisayar oluşturulduğunda, uygulama tarafından okunan son ortam ayarı uygulamanın ortamını belirler.</span><span class="sxs-lookup"><span data-stu-id="0ac60-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="0ac60-158">Uygulama çalışırken uygulamanın ortamı değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="0ac60-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="0ac60-159">Ortam değişkeni veya platform ayarı</span><span class="sxs-lookup"><span data-stu-id="0ac60-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="0ac60-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0ac60-160">Azure App Service</span></span>

<span data-ttu-id="0ac60-161">[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="0ac60-162">**Uygulamayı Uygulama Hizmetleri** bıçağından seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="0ac60-163">**Ayarlar** grubunda **Yapılandırma** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="0ac60-164">Uygulama **ayarları** sekmesinde **Yeni uygulama ayarı'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="0ac60-165">**Ekle/Yap uygulama ayarı** penceresinde, `ASPNETCORE_ENVIRONMENT` **Adı'nı**sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="0ac60-166">**Değer**için, ortamı sağlayın (örneğin, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="0ac60-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="0ac60-167">Dağıtım yuvaları değiş tokuş edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **Dağıtım yuvası ayar** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="0ac60-168">Daha fazla bilgi için bkz. Azure [dokümanında Azure Uygulama Hizmeti'nde hazırlama ortamları ayarlayın.](/azure/app-service/web-sites-staged-publishing)</span><span class="sxs-lookup"><span data-stu-id="0ac60-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="0ac60-169">**Ekle/Yap uygulama ayar** penceresini kapatmak için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="0ac60-170">**Yapılandırma** bıçağının üst kısmında **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="0ac60-171">Azure Uygulama Hizmeti, Azure portalına bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="0ac60-172">Windows</span><span class="sxs-lookup"><span data-stu-id="0ac60-172">Windows</span></span>

<span data-ttu-id="0ac60-173">Uygulama `ASPNETCORE_ENVIRONMENT` [dotnet çalıştır'ı](/dotnet/core/tools/dotnet-run)kullanmaya başladığında geçerli oturumu ayarlamak için aşağıdaki komutlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="0ac60-174">**Komut**</span><span class="sxs-lookup"><span data-stu-id="0ac60-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0ac60-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0ac60-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="0ac60-176">Bu komutlar yalnızca geçerli pencere için etkili olur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="0ac60-177">Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayarına veya makine değerine geri döner.</span><span class="sxs-lookup"><span data-stu-id="0ac60-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="0ac60-178">Windows'da değeri genel olarak ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="0ac60-179">Denetim **Masası** > **Sistemi** > **Gelişmiş sistem ayarlarını** açın `ASPNETCORE_ENVIRONMENT` ve değeri ekleyin veya düzenesin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Sistem Gelişmiş Özellikleri](environments/_static/systemsetting_environment.png)

  ![ASPNET Çekirdek Çevre Değişkeni](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="0ac60-182">İdari komut istemini açın `setx` ve komutu kullanın veya bir `[Environment]::SetEnvironmentVariable`yönetim PowerShell komut istemi ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="0ac60-183">**Komut**</span><span class="sxs-lookup"><span data-stu-id="0ac60-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="0ac60-184">Anahtar, `/M` sistem düzeyinde ortam değişkenini ayarlamak için gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0ac60-185">`/M` Anahtar kullanılmazsa, ortam değişkeni kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="0ac60-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0ac60-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="0ac60-187">Seçenek `Machine` değeri, sistem düzeyinde ortam değişkenini ayarlamak için gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0ac60-188">Seçenek değeri `User`, ortam değişkeni kullanıcı hesabı için ayarlanır değiştirilirse.</span><span class="sxs-lookup"><span data-stu-id="0ac60-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="0ac60-189">Ortam `ASPNETCORE_ENVIRONMENT` değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde etkili olur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="0ac60-190">**Config**</span><span class="sxs-lookup"><span data-stu-id="0ac60-190">**web.config**</span></span>

<span data-ttu-id="0ac60-191">`ASPNETCORE_ENVIRONMENT` *web.config*ile ortam değişkenini ayarlamak için, ortam <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> *değişkenlerini ayarlama* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="0ac60-192">**Proje dosyası veya yayımlama profili**</span><span class="sxs-lookup"><span data-stu-id="0ac60-192">**Project file or publish profile**</span></span>

<span data-ttu-id="0ac60-193">**Windows IIS dağıtımları için:** `<EnvironmentName>` Özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0ac60-194">Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="0ac60-195">**IIS Başına Uygulama Havuzu**</span><span class="sxs-lookup"><span data-stu-id="0ac60-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="0ac60-196">Yalıtılmış bir Uygulama Havuzunda çalışan bir uygulama için `ASPNETCORE_ENVIRONMENT` ortam değişkenini ayarlamak için (IIS 10.0 veya sonraki saatlerde desteklenen), Çevre [Değişkenleri ortamıDeğişkenler &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="0ac60-197">Ortam `ASPNETCORE_ENVIRONMENT` değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0ac60-198">IIS'de bir uygulamayı barındırırken `ASPNETCORE_ENVIRONMENT` ve ortam değişkenini eklerken veya değiştirirken, yeni değerin uygulamalar tarafından alınması için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="0ac60-199">Bir `net stop was /y` komut `net start w3svc` istemi tarafından takip yürütme.</span><span class="sxs-lookup"><span data-stu-id="0ac60-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="0ac60-200">Sunucuyu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="0ac60-201">macOS</span><span class="sxs-lookup"><span data-stu-id="0ac60-201">macOS</span></span>

<span data-ttu-id="0ac60-202">macOS için geçerli ortamı ayarlama, uygulamayı çalıştırırken satır içi olarak gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="0ac60-203">Alternatif olarak, uygulamayı `export` çalıştırmadan önce ortamı ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0ac60-204">Makine düzeyinde ortam değişkenleri *.bashrc* veya *.bash_profile* dosyasında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="0ac60-205">Herhangi bir metin düzenleyicisini kullanarak dosyayı edin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-205">Edit the file using any text editor.</span></span> <span data-ttu-id="0ac60-206">Aşağıdaki ifadeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="0ac60-207">Linux</span><span class="sxs-lookup"><span data-stu-id="0ac60-207">Linux</span></span>

<span data-ttu-id="0ac60-208">Linux dağıtımları için, `export` oturum tabanlı değişken ayarları için komut istemikomutunu ve makine düzeyinde ortam ayarları için *bash_profile* dosyayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="0ac60-209">Ortamı kodda ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ac60-209">Set the environment in code</span></span>

<span data-ttu-id="0ac60-210">Ev <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> sahibini kurarken arayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="0ac60-211">Bkz. <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="0ac60-212">Ortama göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0ac60-212">Configuration by environment</span></span>

<span data-ttu-id="0ac60-213">Yapılandırmayı ortama göre yüklemek için şunları öneririz:</span><span class="sxs-lookup"><span data-stu-id="0ac60-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="0ac60-214">*uygulama ayarları* dosyaları (*appsettings.{ Çevre}.json*).</span><span class="sxs-lookup"><span data-stu-id="0ac60-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="0ac60-215">Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="0ac60-216">Ortam değişkenleri (uygulamanın barındırıldığı her sistemde ayarlayın).</span><span class="sxs-lookup"><span data-stu-id="0ac60-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="0ac60-217">Bakın <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>ve .</span><span class="sxs-lookup"><span data-stu-id="0ac60-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="0ac60-218">Gizli Yönetici (yalnızca Geliştirme ortamında).</span><span class="sxs-lookup"><span data-stu-id="0ac60-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="0ac60-219">Bkz. <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="0ac60-220">Çevre tabanlı Başlangıç sınıfı ve yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0ac60-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="0ac60-221">Inject IWebHostEnvironment içine Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="0ac60-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="0ac60-222">Enjekte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure`edin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="0ac60-223">Bu yaklaşım, uygulama yalnızca ortam `Startup.Configure` başına en az kod farkı olan birkaç ortam için ayarlama gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="0ac60-224">Başlangıç sınıfına IWebHostEnvironment enjekte edin</span><span class="sxs-lookup"><span data-stu-id="0ac60-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="0ac60-225">Yapıcıya <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="0ac60-226">Bu yaklaşım, uygulama ortam başına `Startup` en az kod farkı olan yalnızca birkaç ortam için yapılandırma gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="0ac60-227">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="0ac60-227">In the following example:</span></span>

* <span data-ttu-id="0ac60-228">Çevre `_env` alanında tutulur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="0ac60-229">`_env`uygulamanın `ConfigureServices` ortamına göre başlangıç yapılandırmasında kullanılır. `Configure`</span><span class="sxs-lookup"><span data-stu-id="0ac60-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a><span data-ttu-id="0ac60-230">Başlangıç sınıfı kuralları</span><span class="sxs-lookup"><span data-stu-id="0ac60-230">Startup class conventions</span></span>

<span data-ttu-id="0ac60-231">bir ASP.NET Core uygulaması başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyüklemeye yakalar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="0ac60-232">Uygulama farklı ortamlar için ayrı `Startup` sınıflar `StartupDevelopment`tanımlayabilir (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ac60-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="0ac60-233">Çalışma `Startup` zamanında uygun sınıf seçilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="0ac60-234">Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="0ac60-235">Eşleşen `Startup{EnvironmentName}` bir sınıf bulunamazsa, `Startup` sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="0ac60-236">Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="0ac60-237">Çevre tabanlı `Startup` sınıfları uygulamak `Startup{EnvironmentName}` için, kullanılan her ortam için `Startup` bir sınıf ve bir geri dönüş sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ac60-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="0ac60-238">Bir derleme adını kabul eden [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="0ac60-239">Başlangıç yöntemi kuralları</span><span class="sxs-lookup"><span data-stu-id="0ac60-239">Startup method conventions</span></span>

<span data-ttu-id="0ac60-240">[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [YapılandırmaHizmetleri,](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) formun `Configure<EnvironmentName>` ortama özgü `Configure<EnvironmentName>Services`sürümlerini destekler ve.</span><span class="sxs-lookup"><span data-stu-id="0ac60-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="0ac60-241">Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="0ac60-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0ac60-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0ac60-243">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0ac60-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0ac60-244">ASP.NET Core, bir ortam değişkenini kullanarak uygulama davranışını çalışma zamanı ortamına göre yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="0ac60-245">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ac60-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="0ac60-246">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="0ac60-246">Environments</span></span>

<span data-ttu-id="0ac60-247">ASP.NET Core uygulama `ASPNETCORE_ENVIRONMENT` başlangıç çevre değişkenokur ve [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)değeri depolar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="0ac60-248">`ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak üç değer çerçeve tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="0ac60-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(varsayılan)</span><span class="sxs-lookup"><span data-stu-id="0ac60-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="0ac60-250">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0ac60-250">The preceding code:</span></span>

* <span data-ttu-id="0ac60-251">Çağırır [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) olarak ayarlandığında `ASPNETCORE_ENVIRONMENT` `Development`.</span><span class="sxs-lookup"><span data-stu-id="0ac60-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="0ac60-252">Değeri `ASPNETCORE_ENVIRONMENT` aşağıdakilerden biri ayarlandığında [UseExceptionHandler'ı](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) çağırır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="0ac60-253">[Çevre Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` öğeye biçimlendirme eklemek veya hariç tutmak için aşağıdakileri kullanır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="0ac60-254">Windows ve macOS'ta ortam değişkenleri ve değerleri büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="0ac60-255">Linux ortamı değişkenleri ve değerleri varsayılan olarak **büyük/küçük harf duyarlıdır.**</span><span class="sxs-lookup"><span data-stu-id="0ac60-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="0ac60-256">Geliştirme</span><span class="sxs-lookup"><span data-stu-id="0ac60-256">Development</span></span>

<span data-ttu-id="0ac60-257">Geliştirme ortamı, üretimde açığa çıkmaması gereken özellikleri etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="0ac60-258">Örneğin, ASP.NET Çekirdek şablonları geliştirme ortamında [Geliştirici Özel Durum Sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="0ac60-259">Yerel makine geliştirme ortamı projenin *Properties\launchSettings.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="0ac60-260">*launchSettings.json'da* ayarlanan ortam değerleri sistem ortamında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="0ac60-261">Aşağıdaki JSON *bir launchSettings.json* dosyasından üç profilleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="0ac60-262">`applicationUrl` *launchSettings.json'daki* özellik, sunucu URL'lerinin bir listesini belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="0ac60-263">Listedeki URL'ler arasında bir yarı nokta kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-263">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="0ac60-264">[Uygulama dotnet çalıştır](/dotnet/core/tools/dotnet-run)ı ile başlatıldığında, `"commandName": "Project"` ilk profil kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="0ac60-265">Değer başlatmak `commandName` için web sunucusu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="0ac60-266">`commandName`aşağıdakilerden herhangi biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="0ac60-267">`Project`(Hangi Kerkenez başlattı)</span><span class="sxs-lookup"><span data-stu-id="0ac60-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="0ac60-268">Bir uygulama [dotnet çalıştırılan](/dotnet/core/tools/dotnet-run)başlatıldığında:</span><span class="sxs-lookup"><span data-stu-id="0ac60-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="0ac60-269">*launchSettings.json* varsa okunur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="0ac60-270">`environmentVariables`*launchSettings.json'daki* ayarlar ortam değişkenlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="0ac60-271">Barındırma ortamı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="0ac60-272">Aşağıdaki [çıktı, dotnet çalıştırıile](/dotnet/core/tools/dotnet-run)başlayan bir uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="0ac60-273">Visual Studio proje özellikleri **Hata ayıklama** sekmesi *launchSettings.json* dosyasını düzenlemek için bir GUI sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proje Özellikleri Ayar Ortamı değişkenleri](environments/_static/project-properties-debug.png)

<span data-ttu-id="0ac60-275">Proje profillerinde yapılan değişiklikler, web sunucusu yeniden başlatılına kadar etkili olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="0ac60-276">Kerkenez, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="0ac60-277">*launchSettings.json* sırları saklamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="0ac60-278">[Gizli Yönetici aracı](xref:security/app-secrets) yerel gelişim için sırları depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="0ac60-279">Visual [Studio Code](https://code.visualstudio.com/)kullanırken ortam değişkenleri *.vscode/launch.json* dosyasında ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="0ac60-280">Aşağıdaki örnek, ortamı `Development`şu şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-280">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="0ac60-281">Projedeki *bir .vscode/launch.json* dosyası, uygulama `dotnet run` nın *özellikleri/launchSettings.json*ile aynı şekilde başlatıldığında okunmaz.</span><span class="sxs-lookup"><span data-stu-id="0ac60-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="0ac60-282">*LaunchSettings.json* dosyası olmayan geliştirme aşamasında bir uygulama başlatırken, ortamı bir ortam değişkeni veya `dotnet run` komut satırı bağımsız değişkeniyle komuta ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="0ac60-283">Üretim</span><span class="sxs-lookup"><span data-stu-id="0ac60-283">Production</span></span>

<span data-ttu-id="0ac60-284">Üretim ortamı, güvenliği, performansı ve uygulama sağlamlığını en üst düzeye çıkaracak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="0ac60-285">Geliştirmeden farklı olan bazı yaygın ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="0ac60-286">Önbelleğe alma.</span><span class="sxs-lookup"><span data-stu-id="0ac60-286">Caching.</span></span>
* <span data-ttu-id="0ac60-287">İstemci tarafı kaynakları bir CDN'den paketlenir, minified edilir ve potansiyel olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="0ac60-288">Tanılama hata sayfaları devre dışı.</span><span class="sxs-lookup"><span data-stu-id="0ac60-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="0ac60-289">Dost hata sayfaları etkin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="0ac60-290">Üretim günlüğü ve izleme etkin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="0ac60-291">Örneğin, [Uygulama Öngörüleri.](/azure/application-insights/app-insights-asp-net-core)</span><span class="sxs-lookup"><span data-stu-id="0ac60-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0ac60-292">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ac60-292">Set the environment</span></span>

<span data-ttu-id="0ac60-293">Bir ortam değişkeni veya platform ayarı ile sınama için belirli bir ortam ayarlamak genellikle yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="0ac60-294">Ortam ayarlanmıyorsa, varsayılan olarak `Production`hata ayıklama özelliklerinin çoğunu devre dışı bırakmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="0ac60-295">Ortamı ayarlama yöntemi işletim sistemine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="0ac60-296">Ana bilgisayar oluşturulduğunda, uygulama tarafından okunan son ortam ayarı uygulamanın ortamını belirler.</span><span class="sxs-lookup"><span data-stu-id="0ac60-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="0ac60-297">Uygulama çalışırken uygulamanın ortamı değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="0ac60-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="0ac60-298">Ortam değişkeni veya platform ayarı</span><span class="sxs-lookup"><span data-stu-id="0ac60-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="0ac60-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0ac60-299">Azure App Service</span></span>

<span data-ttu-id="0ac60-300">[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="0ac60-301">**Uygulamayı Uygulama Hizmetleri** bıçağından seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="0ac60-302">**Ayarlar** grubunda **Yapılandırma** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="0ac60-303">Uygulama **ayarları** sekmesinde **Yeni uygulama ayarı'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="0ac60-304">**Ekle/Yap uygulama ayarı** penceresinde, `ASPNETCORE_ENVIRONMENT` **Adı'nı**sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="0ac60-305">**Değer**için, ortamı sağlayın (örneğin, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="0ac60-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="0ac60-306">Dağıtım yuvaları değiş tokuş edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **Dağıtım yuvası ayar** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="0ac60-307">Daha fazla bilgi için bkz. Azure [dokümanında Azure Uygulama Hizmeti'nde hazırlama ortamları ayarlayın.](/azure/app-service/web-sites-staged-publishing)</span><span class="sxs-lookup"><span data-stu-id="0ac60-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="0ac60-308">**Ekle/Yap uygulama ayar** penceresini kapatmak için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="0ac60-309">**Yapılandırma** bıçağının üst kısmında **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="0ac60-310">Azure Uygulama Hizmeti, Azure portalına bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="0ac60-311">Windows</span><span class="sxs-lookup"><span data-stu-id="0ac60-311">Windows</span></span>

<span data-ttu-id="0ac60-312">Uygulama `ASPNETCORE_ENVIRONMENT` [dotnet çalıştır'ı](/dotnet/core/tools/dotnet-run)kullanmaya başladığında geçerli oturumu ayarlamak için aşağıdaki komutlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0ac60-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="0ac60-313">**Komut**</span><span class="sxs-lookup"><span data-stu-id="0ac60-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0ac60-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0ac60-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="0ac60-315">Bu komutlar yalnızca geçerli pencere için etkili olur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="0ac60-316">Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayarına veya makine değerine geri döner.</span><span class="sxs-lookup"><span data-stu-id="0ac60-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="0ac60-317">Windows'da değeri genel olarak ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="0ac60-318">Denetim **Masası** > **Sistemi** > **Gelişmiş sistem ayarlarını** açın `ASPNETCORE_ENVIRONMENT` ve değeri ekleyin veya düzenesin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Sistem Gelişmiş Özellikleri](environments/_static/systemsetting_environment.png)

  ![ASPNET Çekirdek Çevre Değişkeni](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="0ac60-321">İdari komut istemini açın `setx` ve komutu kullanın veya bir `[Environment]::SetEnvironmentVariable`yönetim PowerShell komut istemi ve kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="0ac60-322">**Komut**</span><span class="sxs-lookup"><span data-stu-id="0ac60-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="0ac60-323">Anahtar, `/M` sistem düzeyinde ortam değişkenini ayarlamak için gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0ac60-324">`/M` Anahtar kullanılmazsa, ortam değişkeni kullanıcı hesabı için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="0ac60-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0ac60-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="0ac60-326">Seçenek `Machine` değeri, sistem düzeyinde ortam değişkenini ayarlamak için gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0ac60-327">Seçenek değeri `User`, ortam değişkeni kullanıcı hesabı için ayarlanır değiştirilirse.</span><span class="sxs-lookup"><span data-stu-id="0ac60-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="0ac60-328">Ortam `ASPNETCORE_ENVIRONMENT` değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde etkili olur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="0ac60-329">**Config**</span><span class="sxs-lookup"><span data-stu-id="0ac60-329">**web.config**</span></span>

<span data-ttu-id="0ac60-330">`ASPNETCORE_ENVIRONMENT` *web.config*ile ortam değişkenini ayarlamak için, ortam <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> *değişkenlerini ayarlama* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="0ac60-331">**Proje dosyası veya yayımlama profili**</span><span class="sxs-lookup"><span data-stu-id="0ac60-331">**Project file or publish profile**</span></span>

<span data-ttu-id="0ac60-332">**Windows IIS dağıtımları için:** `<EnvironmentName>` Özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0ac60-333">Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0ac60-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="0ac60-334">**IIS Başına Uygulama Havuzu**</span><span class="sxs-lookup"><span data-stu-id="0ac60-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="0ac60-335">Yalıtılmış bir Uygulama Havuzunda çalışan bir uygulama için `ASPNETCORE_ENVIRONMENT` ortam değişkenini ayarlamak için (IIS 10.0 veya sonraki saatlerde desteklenen), Çevre [Değişkenleri ortamıDeğişkenler &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="0ac60-336">Ortam `ASPNETCORE_ENVIRONMENT` değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0ac60-337">IIS'de bir uygulamayı barındırırken `ASPNETCORE_ENVIRONMENT` ve ortam değişkenini eklerken veya değiştirirken, yeni değerin uygulamalar tarafından alınması için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="0ac60-338">Bir `net stop was /y` komut `net start w3svc` istemi tarafından takip yürütme.</span><span class="sxs-lookup"><span data-stu-id="0ac60-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="0ac60-339">Sunucuyu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="0ac60-340">macOS</span><span class="sxs-lookup"><span data-stu-id="0ac60-340">macOS</span></span>

<span data-ttu-id="0ac60-341">macOS için geçerli ortamı ayarlama, uygulamayı çalıştırırken satır içi olarak gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="0ac60-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="0ac60-342">Alternatif olarak, uygulamayı `export` çalıştırmadan önce ortamı ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0ac60-343">Makine düzeyinde ortam değişkenleri *.bashrc* veya *.bash_profile* dosyasında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="0ac60-344">Herhangi bir metin düzenleyicisini kullanarak dosyayı edin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-344">Edit the file using any text editor.</span></span> <span data-ttu-id="0ac60-345">Aşağıdaki ifadeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ac60-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="0ac60-346">Linux</span><span class="sxs-lookup"><span data-stu-id="0ac60-346">Linux</span></span>

<span data-ttu-id="0ac60-347">Linux dağıtımları için, `export` oturum tabanlı değişken ayarları için komut istemikomutunu ve makine düzeyinde ortam ayarları için *bash_profile* dosyayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="0ac60-348">Ortamı kodda ayarlama</span><span class="sxs-lookup"><span data-stu-id="0ac60-348">Set the environment in code</span></span>

<span data-ttu-id="0ac60-349">Ev <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> sahibini kurarken arayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="0ac60-350">Bkz. <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="0ac60-351">Ortama göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0ac60-351">Configuration by environment</span></span>

<span data-ttu-id="0ac60-352">Yapılandırmayı ortama göre yüklemek için şunları öneririz:</span><span class="sxs-lookup"><span data-stu-id="0ac60-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="0ac60-353">*uygulama ayarları* dosyaları (*appsettings.{ Çevre}.json*).</span><span class="sxs-lookup"><span data-stu-id="0ac60-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="0ac60-354">Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="0ac60-355">Ortam değişkenleri (uygulamanın barındırıldığı her sistemde ayarlayın).</span><span class="sxs-lookup"><span data-stu-id="0ac60-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="0ac60-356">Bakın <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>ve .</span><span class="sxs-lookup"><span data-stu-id="0ac60-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="0ac60-357">Gizli Yönetici (yalnızca Geliştirme ortamında).</span><span class="sxs-lookup"><span data-stu-id="0ac60-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="0ac60-358">Bkz. <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="0ac60-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="0ac60-359">Çevre tabanlı Başlangıç sınıfı ve yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0ac60-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="0ac60-360">Inject IHostingEnvironment içine Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="0ac60-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="0ac60-361">Enjekte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup.Configure`edin.</span><span class="sxs-lookup"><span data-stu-id="0ac60-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="0ac60-362">Bu yaklaşım, uygulama yalnızca ortam `Startup.Configure` başına en az kod farkı olan yalnızca birkaç ortam için yapılandırma gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="0ac60-363">Başlangıç sınıfına IHostingEnvironment enjekte edin</span><span class="sxs-lookup"><span data-stu-id="0ac60-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="0ac60-364">Oluşturucuya enjekte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> edin `Startup` ve hizmeti `Startup` sınıf boyunca kullanılmak üzere bir alana atayın.</span><span class="sxs-lookup"><span data-stu-id="0ac60-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="0ac60-365">Bu yaklaşım, uygulama, ortam başına en az kod farkı olan yalnızca birkaç ortam için başlangıç yapılandırması gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="0ac60-366">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="0ac60-366">In the following example:</span></span>

* <span data-ttu-id="0ac60-367">Çevre `_env` alanında tutulur.</span><span class="sxs-lookup"><span data-stu-id="0ac60-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="0ac60-368">`_env`uygulamanın `ConfigureServices` ortamına göre başlangıç yapılandırmasında kullanılır. `Configure`</span><span class="sxs-lookup"><span data-stu-id="0ac60-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="0ac60-369">Başlangıç sınıfı kuralları</span><span class="sxs-lookup"><span data-stu-id="0ac60-369">Startup class conventions</span></span>

<span data-ttu-id="0ac60-370">bir ASP.NET Core uygulaması başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyüklemeye yakalar.</span><span class="sxs-lookup"><span data-stu-id="0ac60-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="0ac60-371">Uygulama farklı ortamlar için ayrı `Startup` sınıflar `StartupDevelopment`tanımlayabilir (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ac60-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="0ac60-372">Çalışma `Startup` zamanında uygun sınıf seçilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="0ac60-373">Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir.</span><span class="sxs-lookup"><span data-stu-id="0ac60-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="0ac60-374">Eşleşen `Startup{EnvironmentName}` bir sınıf bulunamazsa, `Startup` sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="0ac60-375">Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="0ac60-376">Çevre tabanlı `Startup` sınıfları uygulamak `Startup{EnvironmentName}` için, kullanılan her ortam için `Startup` bir sınıf ve bir geri dönüş sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ac60-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="0ac60-377">Bir derleme adını kabul eden [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ac60-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="0ac60-378">Başlangıç yöntemi kuralları</span><span class="sxs-lookup"><span data-stu-id="0ac60-378">Startup method conventions</span></span>

<span data-ttu-id="0ac60-379">[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [YapılandırmaHizmetleri,](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) formun `Configure<EnvironmentName>` ortama özgü `Configure<EnvironmentName>Services`sürümlerini destekler ve.</span><span class="sxs-lookup"><span data-stu-id="0ac60-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="0ac60-380">Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ac60-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="0ac60-381">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0ac60-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
