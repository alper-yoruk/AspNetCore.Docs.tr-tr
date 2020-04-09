---
title: Temel uygulamaları Azure Uygulama Hizmetine ASP.NET dağıtma
author: bradygaster
description: Bu makalede, Azure ana bilgisayar ve dağıtım kaynakları bağlantıları içerir.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665146"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Temel uygulamaları Azure Uygulama Hizmetine ASP.NET dağıtma

[Azure App Service,](https://azure.microsoft.com/services/app-service/) ASP.NET Core dahil olmak üzere web uygulamalarını barındırmak için bir [Microsoft bulut bilgi işlem platformu hizmetidir.](https://azure.microsoft.com/)

## <a name="useful-resources"></a>Yararlı kaynaklar

[Uygulama Hizmeti Dokümantasyonu,](/azure/app-service/) Azure Apps dokümantasyon, öğreticiler, örnekler, nasıl yapılır kılavuzları ve diğer kaynakların yuvasıdır. ASP.NET Core uygulamaları barındırma ile ilgili iki önemli öğreticiler şunlardır:

[Azure’da ASP.NET Core web uygulaması oluşturma](/azure/app-service/app-service-web-get-started-dotnet)  
Windows'daki Azure Uygulama Hizmeti'ne ASP.NET Bir Çekirdek web uygulaması oluşturmak ve dağıtmak için Visual Studio'yı kullanın.

[Linux'ta Uygulama Hizmeti'nde ASP.NET Core uygulaması oluşturun](/azure/app-service/containers/quickstart-dotnetcore)  
Linux'ta Azure Uygulama Hizmeti'ne bir ASP.NET Çekirdek web uygulaması oluşturmak ve dağıtmak için komut satırını kullanın.

Azure App hizmetinde bulunan ASP.NET Core sürümü için [App Service Dashboard'daki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bölümüne bakın.

[Uygulama Hizmeti Duyuruları](https://github.com/Azure/app-service-announcements/) deposuna abone olun ve sorunları izleyin. Uygulama Hizmeti ekibi, Uygulama Hizmeti'ne gelen duyuruları ve senaryoları düzenli olarak yayınlar.

Aşağıdaki makaleler ASP.NET Temel belgelerde mevcuttur:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Visual Studio'ASP.NET bir ASP.NET Core uygulamasını Azure Uygulama Hizmeti'nde nasıl yayınlayacağınızı öğrenin.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Visual Studio'yu kullanarak bir ASP.NET Core web uygulaması oluşturmayı ve sürekli dağıtım için Git'i kullanarak Azure Uygulama Hizmeti'ne nasıl dağıttığı öğrenin.

[İlk işlem hattınızı oluşturma](/azure/devops/pipelines/get-started-yaml)  
ASP.NET Core uygulaması için bir CI yapısı ayarlayın ve ardından Azure Uygulama Hizmeti'ne sürekli dağıtım sürümü oluşturun.

[Azure Web App sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Azure Apps platformu tarafından uygulanan Azure Uygulama Hizmeti çalışma zamanı yürütme sınırlamalarını keşfedin.

<xref:test/troubleshoot>  
ASP.NET Core projeleri ile uyarıları ve hataları anlayın ve sorun giderin.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="platform"></a>Platform

Bir Uygulama Hizmetleri uygulamasının platform mimarisi (x86/x64), A serisi bilgi işlem (Temel) veya daha yüksek barındırma katmanında barındırılan uygulamalar için Uygulamanın Azure Portalı'ndaki ayarlarında ayarlanır. Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [yayımlama profilinde (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Uygulamanın Azure Portalı'ndaki hizmet yapılandırmasındaki ayarla eşleşin.

::: moniker range=">= aspnetcore-2.2"

Azure Uygulama Hizmeti'nde 64 bit (x64) ve 32 bit (x86) uygulamaların çalışma süreleri mevcuttur. App Service'de kullanılabilen [.NET Core SDK](/dotnet/core/sdk) 32 bittir, ancak [Kudu](https://github.com/projectkudu/kudu/wiki) konsolu veya Visual Studio'da yayımlama işlemini kullanarak yerel olarak oluşturulmuş 64 bit uygulamaları dağıtabilirsiniz. Daha fazla bilgi için [Yayımla bölümüne](#publish-and-deploy-the-app) bakın ve uygulama bölümünü dağıtın.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Yerel bağımlılıklara sahip uygulamalar için Azure Uygulama Hizmeti'nde 32 bit (x86) uygulamaların çalışma saatleri bulunur. App Service'de bulunan [.NET Core SDK](/dotnet/core/sdk) 32 bit'tir.

::: moniker-end

.NET Core çalışma zamanı ve .NET Core SDK gibi .NET Çekirdek çerçeve bileşenleri ve dağıtım yöntemleri hakkında daha fazla bilgi için [bkz.](/dotnet/core/about#composition)

### <a name="packages"></a>Paketler

Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için otomatik günlüğe kaydetme özellikleri sağlamak için aşağıdaki NuGet paketlerini ekleyin:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup,](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) Azure Uygulama Hizmeti ile ASP.NET Temel ışık-up tümleştirmesağlamak için [IHostingStartup'ı](xref:fundamentals/configuration/platform-specific-configuration) kullanır. Eklenen günlük özellikleri `Microsoft.AspNetCore.AzureAppServicesIntegration` paket tarafından sağlanır.
* [Microsoft.AspNetCore.AzureAppServicesIntegration,](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) Pakete Azure App Service tanılama günlük sağlayıcıları eklemek için [AddAzureWebAppDiagnostics'i yürütür.](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) `Microsoft.Extensions.Logging.AzureAppServices`
* [Microsoft.Extensions.Logging.AzureAppServices,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) Azure App Service tanılama günlüklerini ve günlük akış özelliklerini desteklemek için logger uygulamaları sağlar.

Önceki paketler [Microsoft.AspNetCore.App metapaketinden](xref:fundamentals/metapackage-app)kullanılamaz. .NET Framework'u hedefleyen `Microsoft.AspNetCore.App` veya meta pakete başvuran uygulamaların, uygulamanın proje dosyasındaki tek tek paketlere açıkça başvurması gerekir.

## <a name="override-app-configuration-using-the-azure-portal"></a>Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın

Azure Portalı'ndaki uygulama ayarları, uygulama için ortam değişkenleri ayarlamanıza izin verir. Çevre değişkenleri [Çevre Değişkenleri Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider)tarafından tüketilebilir.

Azure Portalı'nda bir uygulama ayarı oluşturulduğunda veya değiştirildiğinde ve **Kaydet** düğmesi seçildiğinde, Azure Uygulaması yeniden başlatılır. Ortam değişkeni, hizmet yeniden başlatıldıktan sonra uygulama için kullanılabilir.

::: moniker range=">= aspnetcore-3.0"

Bir uygulama [Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullandığında, ortam değişkenleri <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ana bilgisayarı oluşturmak için çağrıldığında uygulamanın yapılandırmasına yüklenir. Daha fazla bilgi <xref:fundamentals/host/generic-host> için bkz ve [Çevre Değişkenleri Yapılandırma Sağlayıcısı.](xref:fundamentals/configuration/index#environment-variables-configuration-provider)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bir uygulama [Web Host'u](xref:fundamentals/host/web-host)kullandığında, ortam değişkenleri ana <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bilgisayarı oluşturmak için çağrıldığında uygulamanın yapılandırmasına yüklenir. Daha fazla bilgi <xref:fundamentals/host/web-host> için bkz ve [Çevre Değişkenleri Yapılandırma Sağlayıcısı.](xref:fundamentals/configuration/index#environment-variables-configuration-provider)

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken İleriye Dönük Üstbilgileri Ara ware'i yapılandıran [IIS Tümleştirme Aracı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)ve ASP.NET Çekirdek Modülü, düzeni (HTTP/HTTPS) ve isteğin kaynağı olduğu uzak IP adresini iletecek şekilde yapılandırılır. Ek proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

::: moniker range=">= aspnetcore-3.0"

ASP.NET App Service'e dağıtılan Core uygulamaları otomatik olarak bir Uygulama Hizmeti uzantısı alır, **ASP.NET Çekirdek Günlük Tümleştirmesi.** Uzantı, Azure Uygulama Hizmeti'ndeki ASP.NET Core uygulamaları için günlük tümleştirmesini sağlar.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Uygulama Hizmetine dağıtılan Core uygulamaları otomatik olarak bir Uygulama Hizmeti uzantısı alır, **ASP.NET Core Günlük Uzantıları.** Uzantı, Azure Uygulama Hizmeti'ndeki ASP.NET Core uygulamaları için günlük tümleştirmesini sağlar.

::: moniker-end

Bilgileri izlemek, günlüğe kaydetme ve sorun giderme için aşağıdaki makalelere bakın:

[Azure Uygulama Hizmeti'ndeki uygulamaları izleme](/azure/app-service/web-sites-monitor)  
Uygulamalar ve Uygulama Hizmeti planları için kotaları ve ölçümleri nasıl inceleyiş olarak değerlendirebilirsiniz.

[Azure Uygulama Hizmeti'ndeki uygulamalar için tanılama günlüğe kaydetmeyi etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log)  
HTTP durum kodları, başarısız istekler ve web sunucusu etkinliği için tanıgünlüğe nasıl erişileni ve etkinleştirmeyi keşfedin.

<xref:fundamentals/error-handling>  
ASP.NET Core uygulamalarında işleme hatalarına yönelik yaygın yaklaşımları anlayın.

<xref:test/troubleshoot-azure-iis>  
Azure Uygulama Hizmeti dağıtımlarıyla ilgili sorunları ASP.NET Core uygulamalarıyla nasıl tanılayın öğrenin.

<xref:host-and-deploy/azure-iis-errors-reference>  
Sorun giderme önerileriyle Azure App Service/IIS tarafından barındırılan uygulamalar için yaygın dağıtım yapılandırma hatalarını görün.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Veri Koruma anahtarlık ve dağıtım yuvaları

[Veri Koruma anahtarları](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) *%HOME%\ASP.NET\DataProtection-Keys* klasörüne kalıcıdır. Bu klasör ağ depolama tarafından desteklenen ve uygulamayı barındıran tüm makineler arasında senkronize edilir. Anahtarlar istirahatte korunmuyor. Bu klasör, tek bir dağıtım yuvasındaki bir uygulamanın tüm örneklerine anahtarlık sağlar. Hazırlama ve Üretim gibi ayrı dağıtım yuvaları bir anahtarlık paylaşmaz.

Dağıtım yuvaları arasında geçiş yaparken, veri koruması kullanan herhangi bir sistem, önceki yuvadaki anahtarlık kullanarak depolanan verilerin şifresini çözemez. ASP.NET Çerez Middleware çerezleri korumak için veri koruması kullanır. Bu, kullanıcıların Çerez Ara Ware'ASP.NET standartını kullanan bir uygulamanın dışında oturum alabilmelerine yol açar. Yuvadan bağımsız bir anahtarlık çözümü için, şu gibi harici bir anahtarlık sağlayıcısı kullanın:

* Azure Blob Depolama
* Azure Key Vault
* SQL deposu
* Redis önbelleği

Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>.NET Core önizlemesini kullanan bir ASP.NET Core uygulaması dağıtma

.NET Core'un önizleme sürümü kullanan bir uygulamayı dağıtmak için aşağıdaki kaynaklara bakın. Bu yaklaşımlar, çalışma zamanı kullanılabilir olduğunda ancak SDK Azure Uygulama Hizmeti'ne yüklenmediği zaman da kullanılır.

* [Azure Boru Hatlarını kullanarak .NET Core SDK Sürümünü belirtin](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Bağımsız bir önizleme uygulaması dağıtma](#deploy-a-self-contained-preview-app)
* [Kapsayıcılar için Web Uygulamaları ile Docker'ı kullanma](#use-docker-with-web-apps-for-containers)
* [Önizleme sitesi uzantısını yükleme](#install-the-preview-site-extension)

Azure App hizmetinde bulunan ASP.NET Core sürümü için [App Service Dashboard'daki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bölümüne bakın.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Azure Boru Hatlarını kullanarak .NET Core SDK Sürümünü belirtin

Azure DevOps ile sürekli bir tümleştirme yapısı oluşturmak için [Azure App Service CI/CD senaryolarını](/azure/app-service/deploy-continuous-deployment) kullanın. Azure DevOps yapısı oluşturulduktan sonra, isteğe bağlı olarak yapıyı belirli bir SDK sürümünü kullanacak şekilde yapılandırın. 

#### <a name="specify-the-net-core-sdk-version"></a>.NET Core SDK sürümünü belirtin

Bir Azure DevOps oluşturmak için Uygulama Hizmeti dağıtım merkezini kullanırken, `Restore` `Build`varsayılan `Test`yapı `Publish`ardışık yapısı , , , ve . SDK sürümünü belirtmek için, yeni bir adım eklemek için Aracı iş listesindeki **Ekle (+)** düğmesini seçin. Arama çubuğunda **.NET Core SDK'yı** arayın. 

![.NET Core SDK adımını ekleyin](index/add-sdk-step.png)

Adımı,.NET Core SDK'nın belirtilen sürümünü kullanabilmek için yapıdaki ilk konuma taşıyın. .NET Core SDK sürümünü belirtin. Bu örnekte, SDK `3.0.100`.

![Tamamlanan SDK adımı](index/sdk-step-first-place.png)

Bağımsız bir [dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)yayımlamak için, adımda SCD'yi `Publish` yapılandırın ve [Runtime Tanımlayıcısı'nı (RID)](/dotnet/core/rid-catalog)sağlayın.

![Bağımsız yayımlama](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Bağımsız bir önizleme uygulaması dağıtma

Önizleme çalışma zamanını hedefleyen bağımsız bir [dağıtım (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) dağıtımda önizleme çalışma zamanını taşır.

Bağımsız bir uygulama dağıtılırken:

* Azure Uygulama Hizmeti'ndeki site [önizleme sitesi uzantısı](#install-the-preview-site-extension)gerektirmez.
* Uygulama, [çerçeveye bağımlı bir dağıtım (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd)için yayımlama sırasından farklı bir yaklaşımla yayımlanmalıdır.

[Uygulamayı kendi kendine yeten](#deploy-the-app-self-contained) bölüme dağıt'taki kılavuzu izleyin.

### <a name="use-docker-with-web-apps-for-containers"></a>Kapsayıcılar için Web Uygulamaları ile Docker'ı kullanma

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) en son önizleme Docker görüntülerini içerir. Görüntüler temel görüntü olarak kullanılabilir. Resmi kullanın ve kapsayıcılar için Web Apps'a normal olarak dağıtın.

### <a name="install-the-preview-site-extension"></a>Önizleme sitesi uzantısını yükleme

Önizleme sitesi uzantısını kullanarak bir sorun oluşursa, [bir dotnet/AspNetCore sorunu](https://github.com/dotnet/AspNetCore/issues)açın.

1. Azure Portalı'ndan Uygulama Hizmeti'ne gidin.
1. Web uygulamasını seçin.
1. "Uzantılar" için filtre lemek için arama kutusuna "ex" yazın veya yönetim araçları listesini aşağı kaydırın.
1. **Uzantılar**'ı seçin.
1. **Ekle'yi**seçin.
1. listeden **ASP.NET Core {X.Y} ({x64|x86}) Runtime** `{X.Y}` uzantısını seçin, ASP.NET `{x64|x86}` Core önizleme sürümü ve platformu belirtir.
1. Yasal koşulları kabul etmek için **Tamam'ı** seçin.
1. Uzantıyı yüklemek için **Tamam'ı** seçin.

İşlem tamamlandığında, en son .NET Core önizlemesi yüklenir. Yüklemeyi doğrulayın:

1. **Gelişmiş Araçlar'ı**seçin.
1. **Gelişmiş Araçlarda** **Git'i** seçin.
1. Hata **Ayıklama konsolu** > **PowerShell** menü öğesini seçin.
1. PowerShell komut isteminde aşağıdaki komutu uygulayın. ASP.NET Core çalışma zamanı `{X.Y}` sürümünü ve `{PLATFORM}` platformu komutta yerine:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   x64 `True` önizleme çalışma süresi yüklendiğinde komut geri döner.

> [!NOTE]
> Bir Uygulama Hizmetleri uygulamasının platform mimarisi (x86/x64), A serisi bilgi işlem (Temel) veya daha yüksek barındırma katmanında barındırılan uygulamalar için Uygulamanın Azure Portalı'ndaki ayarlarında ayarlanır. Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [yayımlama profilinde (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Uygulamanın Azure portalındaki hizmet yapılandırmasındaki ayarla eşleşin.
>
> Uygulama işlem modunda çalıştırılırsa ve platform mimarisi 64 bit (x64) için yapılandırılırsa, ASP.NET Çekirdek Modülü varsa 64 bit önizleme çalışma süresini kullanır. Azure Portalını kullanarak **Core {X.Y} (x64) Çalışma Süresi uzantısıASP.NET** yükleyin.
>
> x64 önizleme çalışma süresini yükledikten sonra, yüklemeyi doğrulamak için Azure Kudu PowerShell komut penceresinde aşağıdaki komutu çalıştırın. ASP.NET Core çalışma zamanı `{X.Y}` sürümünü aşağıdaki komutla değiştirin:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> x64 `True` önizleme çalışma süresi yüklendiğinde komut geri döner.

> [!NOTE]
> **ASP.NET Core Uzantıları,** Azure Uygulama Hizmetleri'nde ASP.NET Core için Azure günlüğe kaydetmeyi etkinleştirme gibi ek işlevsellik sağlar. Uzantı Visual Studio'dan dağıtılırken otomatik olarak yüklenir. Uzantı yüklenmiyorsa, uygulama için yükleyin.

**ÖNIZLEME sitesi uzantısını ARM şablonuyla kullanma**

Uygulamaları oluşturmak ve dağıtmak için ARM şablonu kullanılıyorsa, `siteextensions` kaynak türü site uzantısını bir web uygulamasına eklemek için kullanılabilir. Örneğin:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Uygulamayı yayımlama ve dağıtma

::: moniker range=">= aspnetcore-2.2"

64 bit dağıtım için:

* 64 bit uygulama oluşturmak için 64 bit .NET Core SDK kullanın.
* Uygulama Hizmetinin **Yapılandırma** > **Genel ayarlarında** **Platformu** **64 Bit** olarak ayarlayın. Uygulama, platform bitness seçimini etkinleştirmek için bir Temel veya daha yüksek hizmet planı kullanmalıdır.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Uygulama çerçevesine bağlı dağıtma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio araç çubuğundan > **"Yayınla "Yayımla** "Tamamla"yı seçin veya **Solution Explorer'daki** projeyi sağ tıklatın ve **Yayımla'yı**seçin. **Build**
1. **Yayımlama hedef** iletişim kutusunda, **Uygulama Hizmeti'nin** seçildiğini onaylayın.
1. **Gelişmiş**'i seçin. **Yayımla** iletişim kutusu açılır.
1. **Yayımla** iletişim kutusunda:
   * **Sürüm** yapılandırmasının seçildiğini doğrulayın.
   * Dağıtım **Modu** açılır listesini açın ve **Framework'e Bağlı'yı**seçin.
   * Hedef Çalışma **Zamanı**olarak **Taşınabilir'i** seçin.
   * Dağıtım sırasında ek dosyaları kaldırmanız gerekiyorsa, **Dosya Yayımlama Seçenekleri'ni** açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu seçin.
   * **Kaydet'i**seçin.
1. Yayımlama sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya varolan bir siteyi güncelleştirin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Proje dosyasında, [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog)belirtmeyin.

1. Komut kabuğundan, [uygulamayı dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla Serbest Bırak yapılandırmasında yayımlayın. Aşağıdaki örnekte, uygulama çerçeveye bağımlı bir uygulama olarak yayınlanır:

   ```console
   dotnet publish --configuration Release
   ```

1. *Depo gözü/Yayın/{TARGET FRAMEWORK}/yayın* dizininin içeriğini App Service'deki siteye taşıyın. Yerel sabit diskinizden veya ağ paylaşımınızdan *yayımlama* klasörü içeriğini doğrudan Kudu konsolundaki App Service'e sürüklüyorsanız, dosyaları [Kudu](https://github.com/projectkudu/kudu/wiki) konsolundaki `D:\home\site\wwwroot` klasöre sürükleyin.

---

### <a name="deploy-the-app-self-contained"></a>Uygulamayı kendi kendine barındıran dağıtma

[Kendi kendine yeten bir dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)için Visual Studio veya .NET Core CLI'yi kullanın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio araç çubuğundan > **"Yayınla "Yayımla** "Tamamla"yı seçin veya **Solution Explorer'daki** projeyi sağ tıklatın ve **Yayımla'yı**seçin. **Build**
1. **Yayımlama hedef** iletişim kutusunda, **Uygulama Hizmeti'nin** seçildiğini onaylayın.
1. **Gelişmiş**'i seçin. **Yayımla** iletişim kutusu açılır.
1. **Yayımla** iletişim kutusunda:
   * **Sürüm** yapılandırmasının seçildiğini doğrulayın.
   * Dağıtım **Modu** açılır listesini açın ve **Bağımsız İçeriği'ni**seçin.
   * **Hedef Çalışma Zamanı** açılır listesinden hedef çalışma süresini seçin. Varsayılan değer: `win-x86`.
   * Dağıtım sırasında ek dosyaları kaldırmanız gerekiyorsa, **Dosya Yayımlama Seçenekleri'ni** açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu seçin.
   * **Kaydet'i**seçin.
1. Yayımlama sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya varolan bir siteyi güncelleştirin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Proje dosyasında, bir veya daha fazla [Runtime Tanımlayıcısı (RIDs) belirtin.](/dotnet/core/rid-catalog) Tek `<RuntimeIdentifier>` bir RID için (tekil) kullanın veya yarı sütunlu sınırlı bir RID listesi sağlamak için (çoğul) kullanın. `<RuntimeIdentifiers>` Aşağıdaki örnekte, `win-x86` RID belirtilir:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Komut kabuğundan, [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla ana bilgisayar çalışma zamanı için uygulamayı Sürüm yapılandırmasında yayımlayın. Aşağıdaki örnekte, uygulama `win-x86` RID için yayınlanır. `--runtime` Opsiyona sağlanan RID, proje dosyasındaki `<RuntimeIdentifier>` (veya) `<RuntimeIdentifiers>`özellikte sağlanmalıdır.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. *Depo gözü/Yayın/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* dizininin içeriğini App Service'deki siteye taşıyın. Yerel sabit diskinizden veya ağ paylaşımınızdan *yayımlama* klasörü içeriğini doğrudan Kudu konsolundaki App Service'e sürüklüyorsanız, dosyaları Kudu konsolundaki `D:\home\site\wwwroot` klasöre sürükleyin.

---

## <a name="protocol-settings-https"></a>Protokol ayarları (HTTPS)

Güvenli iletişim kuralı bağlamaları, HTTPS üzerinden gelen isteklere yanıt verirken kullanılacak bir sertifika belirtmenize olanak sağlar. Bağlama, belirli bir ana bilgisayar adı için verilen geçerli bir özel sertifika *(.pfx)* gerektirir. Daha fazla bilgi için [Bkz. Öğretici: Varolan özel bir SSL sertifikasını Azure Uygulama Hizmetine bağla.](/azure/app-service/app-service-web-tutorial-custom-ssl)

## <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımlamada *web.config'i* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama <xref:host-and-deploy/iis/transform-webconfig>göre ortam değişkenleri ayarlayın), bkz.

## <a name="additional-resources"></a>Ek kaynaklar

* [App Service’e genel bakış](/azure/app-service/app-service-web-overview)
* [Azure Uygulama Hizmeti: .NET Uygulamalarınızda Ev Sahipliği Yapmak Için En İyi Yer (55 dakikalık genel bakış videosu)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Azure Uygulama Hizmeti tanılama genel bakış](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Windows Server'daki Azure Uygulama [Hizmeti, Internet Bilgi Hizmetleri (IIS)](https://www.iis.net/)kullanır. Aşağıdaki konular temel IIS teknolojisi ile ilgilidir:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server - Geçerli ve önceki sürümler için BT yöneticisi içeriği](/windows-server/windows-server-versions)
