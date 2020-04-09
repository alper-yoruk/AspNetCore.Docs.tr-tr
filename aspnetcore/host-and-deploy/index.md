---
title: ASP.NET Core'u barındırma ve dağıtma
author: rick-anderson
description: Barındırma ortamlarını nasıl ayarlayıp Core uygulamalarını ASP.NET nasıl dağıttığınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 464d19bd63e1f0f06bd7d218e7644afde04a5672
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657922"
---
# <a name="host-and-deploy-aspnet-core"></a>ASP.NET Core'u barındırma ve dağıtma

::: moniker range=">= aspnetcore-2.2"

Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:

* Yayımlanmış uygulamayı barındırma sunucusundaki bir klasöre dağıtın.
* İstekler geldiğinde uygulamayı başlatan ve uygulama çöktükten veya sunucu yeniden başlattıktan sonra uygulamayı yeniden başlatan bir işlem yöneticisi ayarlayın.
* Ters proxy yapılandırması için, istekleri uygulamaya iletmek için bir ters proxy ayarlayın.

## <a name="publish-to-a-folder"></a>Klasörde yayımlama

[Dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *yayımlama* klasörüne çalıştırmak için gereken dosyaları kopyalar. Visual Studio'dan dağıtılırken, dosyalar dağıtım hedefine kopyalanmadan önce `dotnet publish` adım otomatik olarak gerçekleşir.

### <a name="folder-contents"></a>Klasör içeriği

*Yayımlama* klasörü bir veya daha fazla uygulama derleme dosyaları, bağımlılıklar ve isteğe bağlı olarak .NET çalışma zamanı içerir.

Bir .NET Core *uygulaması, bağımsız dağıtım* veya *çerçeveye bağımlı dağıtım*olarak yayınlanabilir. Uygulama bağımsızsa, .NET çalışma saatini içeren derleme dosyaları *yayımlama* klasörüne dahil edilir. Uygulama çerçeveye bağlıysa, .NET çalışma zamanı dosyaları dahil değildir, çünkü uygulamasunucuda yüklü olan .NET sürümüne başvuruda bulunur. Varsayılan dağıtım modeli çerçeveye bağlıdır. Daha fazla bilgi için [bkz.](/dotnet/core/deploying/)

*.exe* ve *.dll* dosyalarına ek olarak, ASP.NET Core uygulamasının *yayımlama* klasörü genellikle yapılandırma dosyaları, statik varlıklar ve MVC görünümleri içerir. Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>İşlem yöneticisi ayarlama

ASP.NET Core uygulaması, bir sunucu çöktürdüğünde başlatılması ve yeniden başlatılması gereken bir konsol uygulamasıdır. Başlatma ve yeniden başlatmayı otomatikleştirmek için bir işlem yöneticisi gerekir. ASP.NET Core için en yaygın işlem yöneticileri şunlardır:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows Hizmeti](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Ters proxy ayarlama

Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucu kullanıyorsa, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), veya [IIS](xref:host-and-deploy/iis/index) ters proxy sunucusu olarak kullanılabilir. Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel'e ileter.

Ters&mdash;proxy sunucusu&mdash;olan veya olmayan yapılandırma desteklenen bir barındırma yapılandırmasıdır. Daha fazla bilgi için, [ters proxy ile Kerkenez'in ne zaman kullanılacağına](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)bakın.

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Ek yapılandırma olmadan, bir uygulamanın düzene (HTTP/HTTPS) ve isteğin kaynaklandığı uzak IP adresine erişimi olmayabilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Dağıtımları otomatikleştirmek için Visual Studio ve MSBuild'i kullanma

Dağıtım genellikle [dotnet yayımlamadan](/dotnet/core/tools/dotnet-publish) bir sunucuya çıktı kopyalamanın yanı sıra ek görevler gerektirir. Örneğin, ek dosyalar gerekebilir veya *yayımlama* klasöründen dışlanabilir. Visual Studio web dağıtımı için MSBuild kullanır ve MSBuild dağıtım sırasında diğer birçok görevi yapacak şekilde özelleştirilebilir. Daha fazla bilgi <xref:host-and-deploy/visual-studio-publish-profiles> için bkz ve [MSBuild ve Team Foundation Build](http://msbuildbook.com/) kitabını kullanarak.

[Uygulamalar, Web Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik Git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak doğrudan Visual Studio'dan Azure Uygulama Hizmeti'ne dağıtılabilir. Azure DevOps Hizmetleri, [Azure Uygulama Hizmetine sürekli dağıtım](/azure/devops/pipelines/targets/webapp)yapmayı destekler. Daha fazla bilgi için ASP.NET [Core ve Azure ile DevOps'ye](xref:azure/devops/index)bakın.

## <a name="publish-to-azure"></a>Azure’da Yayımlama

Visual <xref:tutorials/publish-to-azure-webapp-using-vs> Studio'u kullanarak bir uygulamayı Azure'da nasıl yayınlayacağına ilişkin talimatlar alabilme talimatına bakın. Azure'da ASP.NET [Core web uygulaması oluştur](/azure/app-service/app-service-web-get-started-dotnet)tarafından ek bir örnek sağlanmaktadır.

## <a name="publish-with-msdeploy-on-windows"></a>Windows'da MSDeploy ile yayımlama

<xref:host-and-deploy/visual-studio-publish-profiles> [Dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak Windows komut isteminden de dahil olmak üzere Visual Studio yayımlama profiline sahip bir uygulamanın nasıl yayımlanacağına ilişkin talimatlar alabilme talimatına bakın.

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

*web.config* dosyası tarafından sağlanan yapılandırma ile Internet Information Services (IIS) <xref:host-and-deploy/iis/index>dağıtımları için aşağıdaki makalelere bakın.

## <a name="host-in-a-web-farm"></a>Web grubunda barındırma

Bir web çiftliği ortamında ASP.NET Core uygulamalarını barındırma yapılandırması hakkında bilgi için (örneğin, ölçeklenebilirlik <xref:host-and-deploy/web-farm>için uygulamanızın birden çok örneğinin dağıtımı), bkz.

## <a name="host-on-docker"></a>Docker'da ev sahibi

Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Sistem durumu denetimleri gerçekleştirin

Bir uygulama ve bağımlılıkları üzerinde sistem durumu denetimleri gerçekleştirmek için Sistem Durumu Denetimi Middleware'i kullanın. Daha fazla bilgi için bkz. <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:

* Yayımlanmış uygulamayı barındırma sunucusundaki bir klasöre dağıtın.
* İstekler geldiğinde uygulamayı başlatan ve uygulama çöktükten veya sunucu yeniden başlattıktan sonra uygulamayı yeniden başlatan bir işlem yöneticisi ayarlayın.
* Ters proxy yapılandırması için, istekleri uygulamaya iletmek için bir ters proxy ayarlayın.

## <a name="publish-to-a-folder"></a>Klasörde yayımlama

[Dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *yayımlama* klasörüne çalıştırmak için gereken dosyaları kopyalar. Visual Studio'dan dağıtılırken, dosyalar dağıtım hedefine kopyalanmadan önce `dotnet publish` adım otomatik olarak gerçekleşir.

### <a name="folder-contents"></a>Klasör içeriği

*Yayımlama* klasörü bir veya daha fazla uygulama derleme dosyaları, bağımlılıklar ve isteğe bağlı olarak .NET çalışma zamanı içerir.

Bir .NET Core *uygulaması, bağımsız dağıtım* veya *çerçeveye bağımlı dağıtım*olarak yayınlanabilir. Uygulama bağımsızsa, .NET çalışma saatini içeren derleme dosyaları *yayımlama* klasörüne dahil edilir. Uygulama çerçeveye bağlıysa, .NET çalışma zamanı dosyaları dahil değildir, çünkü uygulamasunucuda yüklü olan .NET sürümüne başvuruda bulunur. Varsayılan dağıtım modeli çerçeveye bağlıdır. Daha fazla bilgi için [bkz.](/dotnet/core/deploying/)

*.exe* ve *.dll* dosyalarına ek olarak, ASP.NET Core uygulamasının *yayımlama* klasörü genellikle yapılandırma dosyaları, statik varlıklar ve MVC görünümleri içerir. Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>İşlem yöneticisi ayarlama

ASP.NET Core uygulaması, bir sunucu çöktürdüğünde başlatılması ve yeniden başlatılması gereken bir konsol uygulamasıdır. Başlatma ve yeniden başlatmayı otomatikleştirmek için bir işlem yöneticisi gerekir. ASP.NET Core için en yaygın işlem yöneticileri şunlardır:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows Hizmeti](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Ters proxy ayarlama

Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucu kullanıyorsa, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), veya [IIS](xref:host-and-deploy/iis/index) ters proxy sunucusu olarak kullanılabilir. Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel'e ileter.

Ters&mdash;proxy sunucusu&mdash;olan veya olmayan yapılandırma desteklenen bir barındırma yapılandırmasıdır. Daha fazla bilgi için, [ters proxy ile Kerkenez'in ne zaman kullanılacağına](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)bakın.

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Ek yapılandırma olmadan, bir uygulamanın düzene (HTTP/HTTPS) ve isteğin kaynaklandığı uzak IP adresine erişimi olmayabilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Dağıtımları otomatikleştirmek için Visual Studio ve MSBuild'i kullanma

Dağıtım genellikle [dotnet yayımlamadan](/dotnet/core/tools/dotnet-publish) bir sunucuya çıktı kopyalamanın yanı sıra ek görevler gerektirir. Örneğin, ek dosyalar gerekebilir veya *yayımlama* klasöründen dışlanabilir. Visual Studio web dağıtımı için MSBuild kullanır ve MSBuild dağıtım sırasında diğer birçok görevi yapacak şekilde özelleştirilebilir. Daha fazla bilgi <xref:host-and-deploy/visual-studio-publish-profiles> için bkz ve [MSBuild ve Team Foundation Build](http://msbuildbook.com/) kitabını kullanarak.

[Uygulamalar, Web Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik Git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak doğrudan Visual Studio'dan Azure Uygulama Hizmeti'ne dağıtılabilir. Azure DevOps Hizmetleri, [Azure Uygulama Hizmetine sürekli dağıtım](/azure/devops/pipelines/targets/webapp)yapmayı destekler. Daha fazla bilgi için ASP.NET [Core ve Azure ile DevOps'ye](xref:azure/devops/index)bakın.

## <a name="publish-to-azure"></a>Azure’da Yayımlama

Visual <xref:tutorials/publish-to-azure-webapp-using-vs> Studio'u kullanarak bir uygulamayı Azure'da nasıl yayınlayacağına ilişkin talimatlar alabilme talimatına bakın. Azure'da ASP.NET [Core web uygulaması oluştur](/azure/app-service/app-service-web-get-started-dotnet)tarafından ek bir örnek sağlanmaktadır.

## <a name="publish-with-msdeploy-on-windows"></a>Windows'da MSDeploy ile yayımlama

<xref:host-and-deploy/visual-studio-publish-profiles> [Dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak Windows komut isteminden de dahil olmak üzere Visual Studio yayımlama profiline sahip bir uygulamanın nasıl yayımlanacağına ilişkin talimatlar alabilme talimatına bakın.

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

*web.config* dosyası tarafından sağlanan yapılandırma ile Internet Information Services (IIS) <xref:host-and-deploy/iis/index>dağıtımları için aşağıdaki makalelere bakın.

## <a name="host-in-a-web-farm"></a>Web grubunda barındırma

Bir web çiftliği ortamında ASP.NET Core uygulamalarını barındırma yapılandırması hakkında bilgi için (örneğin, ölçeklenebilirlik <xref:host-and-deploy/web-farm>için uygulamanızın birden çok örneğinin dağıtımı), bkz.

## <a name="host-on-docker"></a>Docker'da ev sahibi

Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
