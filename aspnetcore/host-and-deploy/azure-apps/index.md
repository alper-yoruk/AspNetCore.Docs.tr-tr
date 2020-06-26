---
title: ASP.NET Core uygulamalarını Azure App Service dağıtma
author: bradygaster
description: Bu makale, Azure ana bilgisayarına bağlantılar ve kaynakları dağıtmak için bağlantı içerir.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399315"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>ASP.NET Core uygulamalarını Azure App Service dağıtma

[Azure App Service](https://azure.microsoft.com/services/app-service/) , Web uygulamalarını barındırmak için ASP.NET Core dahil olmak üzere bir [Microsoft bulut bilgi işlem platformu hizmetidir](https://azure.microsoft.com/) .

## <a name="useful-resources"></a>Yararlı kaynaklar

[App Service belge](/azure/app-service/) , Azure Apps belgelerinin, öğreticilerin, örneklerin, nasıl yapılır kılavuzlarının ve diğer kaynakların ana adresidir. Barındırma ASP.NET Core uygulamaları ile ilgili iki önemli öğretici şunlardır:

[Azure’da ASP.NET Core web uygulaması oluşturma](/azure/app-service/app-service-web-get-started-dotnet)  
ASP.NET Core bir Web uygulamasını Windows üzerinde Azure App Service oluşturmak ve dağıtmak için Visual Studio 'Yu kullanın.

[Linux üzerinde App Service ASP.NET Core uygulama oluşturma](/azure/app-service/containers/quickstart-dotnetcore)  
Linux üzerinde Azure App Service için bir ASP.NET Core Web uygulaması oluşturup dağıtmak üzere komut satırını kullanın.

Azure App Service 'te bulunan ASP.NET Core sürümü için [App Service panosundaki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bakın.

[App Service Duyurular](https://github.com/Azure/app-service-announcements/) deposuna abone olun ve sorunları izleyin. App Service takım, App Service gelen duyuruları ve senaryoları düzenli olarak gönderir.

Aşağıdaki makaleler ASP.NET Core belgelerinde sunulmaktadır:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
ASP.NET Core uygulamasının Visual Studio kullanarak Azure App Service nasıl yayımlanacağını öğrenin.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Visual Studio kullanarak ASP.NET Core bir Web uygulaması oluşturmayı ve sürekli dağıtım için git 'i kullanarak Azure App Service nasıl dağıtacağınızı öğrenin.

[İlk işlem hattınızı oluşturma](/azure/devops/pipelines/get-started-yaml)  
ASP.NET Core bir uygulama için CI derlemesi ayarlayın ve Azure App Service için sürekli bir dağıtım sürümü oluşturun.

[Azure Web uygulaması korumalı alanı](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Azure Apps platformu tarafından zorlanan Azure App Service çalışma zamanı yürütme sınırlamalarını bulun.

<xref:test/troubleshoot>  
ASP.NET Core projelerle uyarıları ve hataları anlayın ve sorun giderin.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="platform"></a>Platform

Bir App Services uygulamasının platform mimarisi (x86/x64), A serisi bir işlem (temel) veya daha yüksek bir barındırma katmanında barındırılan uygulamalar için Azure portalında uygulama ayarlarında ayarlanır. Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Azure portalındaki uygulamanın hizmet yapılandırmasındaki ayarla eşleştiğinden emin olun.

::: moniker range=">= aspnetcore-2.2"

64-bit (x64) ve 32-bit (x86) uygulamalarının çalışma zamanları Azure App Service vardır. App Service kullanılabilir [.NET Core SDK](/dotnet/core/sdk) 32 bittir, ancak [kudu](https://github.com/projectkudu/kudu/wiki) konsolunu veya Visual Studio 'daki Yayımla işlemini kullanarak yerel olarak oluşturulan 64 bit uygulamaları dağıtabilirsiniz. Daha fazla bilgi için, [uygulamayı yayımlama ve dağıtma](#publish-and-deploy-the-app) bölümüne bakın.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Yerel bağımlılıklara sahip uygulamalar için, 32-bit (x86) uygulamalarının çalışma zamanları Azure App Service vardır. App Service kullanılabilir [.NET Core SDK](/dotnet/core/sdk) 32 bitlik bir değer.

::: moniker-end

.NET Core Framework bileşenleri ve dağıtım yöntemleri hakkında daha fazla bilgi için, .NET Core çalışma zamanı ve .NET Core SDK hakkında bilgi gibi bkz. [.NET Core: bileşim hakkında](/dotnet/core/about#composition).

### <a name="packages"></a>Paketler

Azure App Service dağıtılan uygulamalar için otomatik günlük oluşturma özellikleri sağlamak üzere aşağıdaki NuGet paketlerini ekleyin:

* [Microsoft. AspNetCore. AzureAppServices. hostingstartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) , Azure App Service ile ASP.NET Core hafif tümleştirme sağlamak Için [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration) kullanır. Eklenen günlük özellikleri, paket tarafından sağlanır `Microsoft.AspNetCore.AzureAppServicesIntegration` .
* [Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) , pakette Azure App Service tanılama günlüğü sağlayıcıları eklemek Için [Addadurewebappdiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) ' i yürütür `Microsoft.Extensions.Logging.AzureAppServices` .
* [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) , Azure App Service tanılama günlüklerini ve günlük akışı özelliklerini desteklemek için günlükçü uygulamaları sağlar.

Önceki paketlere [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)tarafından ulaşılabilir. Metapackage 'i .NET Framework veya başvurusunu hedefleyen uygulamalar, `Microsoft.AspNetCore.App` uygulamanın proje dosyasındaki ayrı paketlere açık olarak başvurmalıdır.

## <a name="override-app-configuration-using-the-azure-portal"></a>Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma

::: moniker range=">= aspnetcore-3.0"

Azure portalındaki uygulama ayarları, uygulamanın ortam değişkenlerini ayarlamanıza olanak sağlar. Ortam değişkenleri, [ortam değişkenleri yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables)tarafından tüketilebilir.

Azure portalında bir uygulama ayarı oluşturulduğunda veya değiştirildiğinde ve **Kaydet** düğmesi seçildiğinde, Azure uygulaması yeniden başlatılır. Ortam değişkeni, hizmet yeniden başlatıldıktan sonra uygulama için kullanılabilir.

Bir uygulama [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullandığında, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> konak oluşturmak için çağrıldığında ortam değişkenleri uygulamanın yapılandırmasına yüklenir. Daha fazla bilgi için bkz <xref:fundamentals/host/generic-host> . ve [ortam değişkenleri yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables).

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Azure portalındaki uygulama ayarları, uygulamanın ortam değişkenlerini ayarlamanıza olanak sağlar. Ortam değişkenleri, [ortam değişkenleri yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider)tarafından tüketilebilir.

Azure portalında bir uygulama ayarı oluşturulduğunda veya değiştirildiğinde ve **Kaydet** düğmesi seçildiğinde, Azure uygulaması yeniden başlatılır. Ortam değişkeni, hizmet yeniden başlatıldıktan sonra uygulama için kullanılabilir.

Bir uygulama [Web konağını](xref:fundamentals/host/web-host)kullandığında, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> konak oluşturmak için çağrıldığında ortam değişkenleri uygulamanın yapılandırmasına yüklenir. Daha fazla bilgi için bkz <xref:fundamentals/host/web-host> . ve [ortam değişkenleri yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında Iletilen üstbilgiler ara yazılımını yapılandıran ve ASP.NET Core modülü DÜZENI (http/https) ve isteğin KAYNAKLANDıĞı uzak IP adresini iletecek şekilde yapılandırılmış [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components). Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

::: moniker range=">= aspnetcore-3.0"

App Service dağıtılan ASP.NET Core uygulamalar, bir App Service uzantısı **ASP.NET Core günlüğe kaydetme tümleştirmesi**otomatik olarak alır. Uzantı, Azure App Service ASP.NET Core uygulamalar için günlüğe kaydetme tümleştirmesi imkanı sunar.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

App Service dağıtılan ASP.NET Core uygulamalar, bir App Service uzantısı **ASP.NET Core günlüğe kaydetme uzantısı**otomatik olarak alır. Uzantı, Azure App Service ASP.NET Core uygulamalar için günlüğe kaydetme tümleştirmesi imkanı sunar.

::: moniker-end

İzleme, günlüğe kaydetme ve sorun giderme bilgileri için aşağıdaki makalelere bakın:

[Azure App Service uygulamaları izleme](/azure/app-service/web-sites-monitor)  
Uygulamalar ve App Service planları için kotaları ve ölçümleri incelemeyi öğrenin.

[Azure App Service uygulamalar için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log)  
HTTP durum kodları, başarısız istekler ve Web sunucusu etkinliği için tanılama günlüğü 'nün nasıl etkinleştirileceğini ve erişebileceğini öğrenin.

<xref:fundamentals/error-handling>  
ASP.NET Core uygulamalarında hataları işlemeye yönelik yaygın yaklaşımları anlayın.

<xref:test/troubleshoot-azure-iis>  
ASP.NET Core uygulamalarla Azure App Service dağıtımlarla ilgili sorunları tanılamayı öğrenin.

<xref:host-and-deploy/azure-iis-errors-reference>  
Sorun giderme önerisi ile Azure App Service/IIS tarafından barındırılan uygulamalar için ortak dağıtım yapılandırma hatalarına bakın.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Veri koruma anahtar halkası ve dağıtım Yuvaları

[Veri koruma anahtarları](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) *%Home%\ASP.NET\DataProtection-Keys* klasöründe kalıcı hale getirilir. Bu klasör, ağ depolama tarafından desteklenir ve uygulamayı barındıran tüm makinelerde eşitlenir. Anahtarlar bekleyen bir şekilde korunmuyor. Bu klasör, bir uygulamanın tüm örneklerine tek bir dağıtım yuvasında anahtar halkasını sağlar. Hazırlama ve üretim gibi ayrı dağıtım yuvaları, anahtar halkasını paylaşmaz.

Dağıtım yuvaları arasında takas edildiğinde, veri koruma kullanan tüm sistem, önceki yuva içindeki anahtar halkasını kullanarak depolanan verilerin şifresini çözemeyecektir. ASP.NET tanımlama bilgisi ara yazılımı, tanımlama bilgilerini korumak için veri koruma kullanır. Bu, kullanıcılara standart ASP.NET tanımlama bilgisi ara yazılımı kullanan bir uygulamanın oturumunu kapatmakta olduğunu gösterir. Yuvada bağımsız bir anahtar halka çözümü için, bir dış anahtar halka sağlayıcısı kullanın, örneğin:

* Azure Blob Depolama
* Azure Key Vault
* SQL Mağazası
* Redis önbelleği

Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>.NET Core önizlemesi kullanan bir ASP.NET Core uygulaması dağıtma

.NET Core 'un önizleme sürümünü kullanan bir uygulamayı dağıtmak için aşağıdaki kaynaklara bakın. Bu yaklaşımlar, çalışma zamanı kullanılabilir ancak SDK Azure App Service üzerine yüklenmemişse de kullanılır.

* [Azure Pipelines kullanarak .NET Core SDK sürümünü belirtin](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Kendi içinde bir önizleme uygulaması dağıtma](#deploy-a-self-contained-preview-app)
* [Kapsayıcılar için Web Apps Docker kullanma](#use-docker-with-web-apps-for-containers)
* [Önizleme sitesi uzantısını yükler](#install-the-preview-site-extension)

Azure App Service 'te bulunan ASP.NET Core sürümü için [App Service panosundaki ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) bakın.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Azure Pipelines kullanarak .NET Core SDK sürümünü belirtin

Azure DevOps ile sürekli tümleştirme derlemesi ayarlamak için [Azure App SERVICE CI/CD senaryoları](/azure/app-service/deploy-continuous-deployment) kullanın. Azure DevOps derlemesi oluşturulduktan sonra, isteğe bağlı olarak derlemeyi belirli bir SDK sürümünü kullanacak şekilde yapılandırın. 

#### <a name="specify-the-net-core-sdk-version"></a>.NET Core SDK sürümünü belirtin

Azure DevOps derlemesi oluşturmak için App Service dağıtım merkezini kullanırken, varsayılan derleme işlem hattı,, ve için adımları içerir `Restore` `Build` `Test` `Publish` . SDK sürümünü belirtmek için, yeni bir adım eklemek üzere aracı iş listesindeki **Ekle (+)** düğmesini seçin. Arama çubuğunda **.NET Core SDK** arayın. 

![.NET Core SDK adımını ekleyin](index/add-sdk-step.png)

Aşağıdaki adımların .NET Core SDK belirtilen sürümünü kullanmasını sağlamak için adımı derlemedeki ilk konuma taşıyın. .NET Core SDK sürümünü belirtin. Bu örnekte, SDK olarak ayarlanır `3.0.100` .

![SDK adımı tamamlandı](index/sdk-step-first-place.png)

[Kendi içindeki bir dağıtımı (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)yayımlamak için, adımında SCD ' yi yapılandırın `Publish` ve [çalışma zamanı tanımlayıcısını (RID)](/dotnet/core/rid-catalog)sağlayın.

![Kendi içinde yayımlama](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Kendi içinde bir önizleme uygulaması dağıtma

Bir önizleme çalışma zamanını hedefleyen [kendinden bağımsız bir dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , dağıtımdaki Önizleme çalışma zamanını taşır.

Kendi kendine içerilen bir uygulama dağıtımında:

* Azure App Service sitesi, [Önizleme sitesi uzantısını](#install-the-preview-site-extension)gerektirmez.
* Uygulamanın, [çerçeveye bağımlı dağıtım (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd)için yayımlarken farklı bir yaklaşımdan sonra yayımlanması gerekir.

[Uygulamanın kendi Içinde dağıtımı](#deploy-the-app-self-contained) bölümünde yer alan yönergeleri izleyin.

### <a name="use-docker-with-web-apps-for-containers"></a>Kapsayıcılar için Web Apps Docker kullanma

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) , en son önizleme Docker görüntülerini içerir. Görüntüler, temel görüntü olarak kullanılabilir. Görüntüyü kullanın ve kapsayıcılar için normal olarak Web Apps için dağıtın.

### <a name="install-the-preview-site-extension"></a>Önizleme sitesi uzantısını yükler

Önizleme sitesi uzantısı kullanılarak bir sorun oluşursa, bir [DotNet/AspNetCore sorunu](https://github.com/dotnet/AspNetCore/issues)açın.

1. Azure portalından App Service gidin.
1. Web uygulamasını seçin.
1. "Uzantıları" filtrelemek için arama kutusuna "Ex" yazın veya yönetim araçları listesini aşağı kaydırın.
1. **Uzantılar**'ı seçin.
1. **Ekle**'yi seçin.
1. Listeden ASP.NET Core önizleme sürümü olduğu ve platformu belirten **ASP.NET Core {X. Y} ({x64 | x86}) çalışma zamanı** uzantısını seçin `{X.Y}` `{x64|x86}` .
1. Yasal koşulları kabul etmek için **Tamam ' ı** seçin.
1. Uzantıyı yüklemek için **Tamam ' ı** seçin.

İşlem tamamlandığında, en son .NET Core önizlemesi yüklenir. Yüklemeyi doğrulayın:

1. **Gelişmiş Araçlar**' ı seçin.
1. **Gelişmiş araçlarda** **Git** ' i seçin.
1. **Hata ayıklama konsolu**  >  **PowerShell** menü öğesini seçin.
1. PowerShell komut isteminde aşağıdaki komutu yürütün. Komutu için ASP.NET Core çalışma zamanı sürümünü `{X.Y}` ve platformunu değiştirin `{PLATFORM}` :

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Bu komut `True` x64 önizlemesi çalışma zamanı yüklendiğinde döndürür.

> [!NOTE]
> Bir App Services uygulamasının platform mimarisi (x86/x64), A serisi bir işlem (temel) veya daha yüksek bir barındırma katmanında barındırılan uygulamalar için Azure portalında uygulama ayarlarında ayarlanır. Uygulamanın yayımlama ayarlarının (örneğin, Visual Studio [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) Azure Portal uygulamanın hizmet yapılandırmasındaki ayarla eşleştiğini doğrulayın.
>
> Uygulama, işlem içi modda çalışıyorsa ve platform mimarisi 64-bit (x64) için yapılandırılmışsa ASP.NET Core modülü, varsa 64 bit önizleme çalışma zamanını kullanır. Azure portalını kullanarak **ASP.NET Core {X. Y} (x64) çalışma zamanı** uzantısını yükler.
>
> X64 Önizleme çalışma zamanını yükledikten sonra, yüklemeyi doğrulamak için Azure kudu PowerShell komut penceresinde aşağıdaki komutu çalıştırın. Aşağıdaki komutta için ASP.NET Core çalışma zamanı sürümünü yerine koyun `{X.Y}` :
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Bu komut `True` x64 önizlemesi çalışma zamanı yüklendiğinde döndürür.

> [!NOTE]
> **ASP.NET Core uzantıları** , Azure Uygulama Hizmetleri 'nde Azure günlük kaydı etkinleştirme gibi ek ASP.NET Core işlevler sunar. Uzantı Visual Studio 'dan dağıtıldığında otomatik olarak yüklenir. Uzantı yüklü değilse, uygulama için bu uygulamayı yükleme.

**Bir ARM şablonuyla önizleme sitesi uzantısını kullanma**

Uygulama oluşturmak ve dağıtmak için bir ARM şablonu kullanılıyorsa, `siteextensions` kaynak türü bir Web uygulamasına site uzantısını eklemek için kullanılabilir. Örneğin:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Uygulamayı yayımlama ve dağıtma

::: moniker range=">= aspnetcore-2.2"

64 bitlik bir dağıtım için:

* 64 bit uygulama derlemek için 64 bit .NET Core SDK kullanın.
* App Service **yapılandırma**genel ayarlarında **platformu** **64 bit** olarak ayarlayın  >  **General settings**. Uygulamanın, platform bit özelliğini tercih etmek için temel veya daha yüksek bir hizmet planı kullanması gerekir.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Uygulama çerçevesine bağımlı dağıtım

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Build**  >  Visual Studio araç çubuğundan derleme**yayımlaması {Application Name}** öğesini seçin veya **Çözüm Gezgini** ' de projeye sağ tıklayıp **Yayımla**' yı seçin.
1. **Bir yayımlama hedefi seç** iletişim kutusunda **App Service** seçili olduğunu onaylayın.
1. **Gelişmiş**'i seçin. **Yayımla** iletişim kutusu açılır.
1. **Yayımla** iletişim kutusunda:
   * **Yayın** yapılandırmasının seçili olduğunu doğrulayın.
   * **Dağıtım modu** açılır listesini açın ve **çerçeveye bağımlı**' ı seçin.
   * **Hedef çalışma zamanı**olarak **Taşınabilir** öğesini seçin.
   * Dağıtımdan sonra ek dosyaları kaldırmanız gerekiyorsa, **dosya yayımlama seçenekleri** ' ni açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu işaretleyin.
   * **Kaydet**’i seçin.
1. Yayımla sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya var olan bir siteyi güncelleştirin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Proje dosyasında, bir [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog)belirtmeyin.

1. Bir komut kabuğundan, uygulamayı sürüm yapılandırmasında [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuyla yayımlayın. Aşağıdaki örnekte, uygulama çerçeveye bağlı bir uygulama olarak yayımlanır:

   ```console
   dotnet publish --configuration Release
   ```

1. *Bin/Release/{Target Framework}/Publish* dizininin içeriğini App Service sitesinde siteye taşıyın. *Klasör içeriğini* yerel sabit sürücünüzden veya ağ paylaşımınızdan, [kudu](https://github.com/projectkudu/kudu/wiki) konsolundaki App Service doğrudan sürüklerseniz, dosyaları `D:\home\site\wwwroot` kudu konsolundaki klasöre sürükleyin.

---

### <a name="deploy-the-app-self-contained"></a>Uygulamayı kendi içinde dağıtma

Visual Studio 'Yu veya kendi içindeki bir dağıtım için .NET Core CLI kullanın [(SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Build**  >  Visual Studio araç çubuğundan derleme**yayımlaması {Application Name}** öğesini seçin veya **Çözüm Gezgini** ' de projeye sağ tıklayıp **Yayımla**' yı seçin.
1. **Bir yayımlama hedefi seç** iletişim kutusunda **App Service** seçili olduğunu onaylayın.
1. **Gelişmiş**'i seçin. **Yayımla** iletişim kutusu açılır.
1. **Yayımla** iletişim kutusunda:
   * **Yayın** yapılandırmasının seçili olduğunu doğrulayın.
   * **Dağıtım modu** açılır listesini açın ve **kendinden bağımsız**' i seçin.
   * Hedef **çalışma** zamanı açılır listesinden hedef çalışma zamanını seçin. Varsayılan değer: `win-x86`.
   * Dağıtımdan sonra ek dosyaları kaldırmanız gerekiyorsa, **dosya yayımlama seçenekleri** ' ni açın ve hedefteki ek dosyaları kaldırmak için onay kutusunu işaretleyin.
   * **Kaydet**’i seçin.
1. Yayımla sihirbazının kalan istemlerini izleyerek yeni bir site oluşturun veya var olan bir siteyi güncelleştirin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Proje dosyasında bir veya daha fazla [çalışma zamanı tanımlayıcısı (RID 'ler)](/dotnet/core/rid-catalog)belirtin. `<RuntimeIdentifier>`Tek BIR RID için (tekil) kullanın veya `<RuntimeIdentifiers>` (plural) kullanarak, noktalı virgülle ayrılmış RID 'ler listesini sağlayın. Aşağıdaki örnekte, `win-x86` RID belirtilir:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Bir komut kabuğundan, uygulamayı, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuyla konağın çalışma zamanının sürüm yapılandırmasında yayımlayın. Aşağıdaki örnekte, uygulama RID için yayımlanır `win-x86` . Seçeneğe sağlanan RID, `--runtime` `<RuntimeIdentifier>` `<RuntimeIdentifiers>` Proje dosyasındaki (veya) özelliğinde sağlanmalıdır.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. *Bin/Release/{Target Framework}/{RUNTIME Identifier}/Publish* dizininin içeriğini App Service sitesinde siteye taşıyın. *Klasör içeriğini* yerel sabit sürücünüzden veya ağ paylaşımınızdan, kudu konsolundaki App Service doğrudan sürüklerseniz, dosyaları `D:\home\site\wwwroot` kudu konsolundaki klasöre sürükleyin.

---

## <a name="protocol-settings-https"></a>Protokol ayarları (HTTPS)

Güvenli Protokol bağlamaları, HTTPS üzerinden isteklere yanıt verme sırasında kullanılacak bir sertifika belirtmenize olanak tanır. Bağlama, belirli bir ana bilgisayar adı için verilen geçerli bir özel sertifika (*. pfx*) gerektirir. Daha fazla bilgi için bkz. [öğretici: var olan bir özel SSL sertifikasını Azure App Service bağlama](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımla *web.config* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlayın), bkz <xref:host-and-deploy/iis/transform-webconfig> ..

## <a name="additional-resources"></a>Ek kaynaklar

* [App Service’e genel bakış](/azure/app-service/app-service-web-overview)
* [Azure App Service: .NET uygulamalarınızı barındırmak için En Iyi yer (55 dakikalık genel bakış videosu)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Azure App Service tanılamada genel bakış](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Windows Server 'da Azure App Service, [Internet Information Services (IIS)](https://www.iis.net/)kullanır. Aşağıdaki konular, temel alınan IIS teknolojisine yöneliktir:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server-geçerli ve önceki sürümler için BT Yöneticisi içeriği](/windows-server/windows-server-versions)
