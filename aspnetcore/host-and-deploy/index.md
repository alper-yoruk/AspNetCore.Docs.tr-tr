---
title: ASP.NET Core barındırma ve dağıtma
author: rick-anderson
description: Barındırma ortamlarını ayarlamayı ve ASP.NET Core uygulamaları dağıtmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/index
ms.openlocfilehash: 79022af0e6ee1294e12c21492bc81bc40482a3fb
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403969"
---
# <a name="host-and-deploy-aspnet-core"></a>ASP.NET Core barındırma ve dağıtma

::: moniker range=">= aspnetcore-2.2"

Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:

* Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.
* İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.
* Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.

## <a name="publish-to-a-folder"></a>Bir klasöre yayımlama

[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar. Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.

### <a name="folder-contents"></a>Klasör içeriği

*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.

.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım*olarak yayımlanabilir. Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir. Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır. Varsayılan dağıtım modeli çerçeveye bağımlıdır. Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).

*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir. Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>İşlem Yöneticisi ayarlama

ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır. Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir. ASP.NET Core için en yaygın işlem yöneticileri şunlardır:

* Linux
  * [NGINX](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows hizmeti](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Ters proxy ayarlama

Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir. Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.

&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması. Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma

Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir. Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz. Visual Studio Web dağıtımı için MSBuild 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir. Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .

Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir. Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler. Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Azure’da Yayımlama

<xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz.. [Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.

## <a name="publish-with-msdeploy-on-windows"></a>Windows 'ta MSDeploy ile Yayımla

Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .

## <a name="host-in-a-web-farm"></a>Web grubunda barındırma

Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..

## <a name="host-on-docker"></a>Docker 'da barındırma

Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Sistem durumu denetimleri gerçekleştirme

Bir uygulamada ve bağımlılıklarında sistem durumu denetimleri gerçekleştirmek için sistem durumu denetimi ara yazılımı kullanın. Daha fazla bilgi için bkz. <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* [ASP.NET barındırma](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Genel olarak, bir ASP.NET Core uygulamasını barındırma ortamına dağıtmak için:

* Yayımlanan uygulamayı barındırma sunucusundaki bir klasöre dağıtın.
* İstekler ulaştığında uygulamayı başlatan bir işlem yöneticisi ayarlayın ve kilitlendikten sonra veya sunucu yeniden başlatıldıktan sonra uygulamayı yeniden başlatır.
* Ters bir ara sunucu yapılandırması için istekleri uygulamaya iletmek üzere ters bir ara sunucu ayarlayın.

## <a name="publish-to-a-folder"></a>Bir klasöre yayımlama

[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu uygulama kodunu derler ve uygulamayı bir *Publish* klasöründe çalıştırmak için gereken dosyaları kopyalar. Visual Studio 'dan dağıtım yaparken, bu `dotnet publish` adım dosyalar dağıtım hedefine kopyalanmadan önce otomatik olarak gerçekleşir.

### <a name="folder-contents"></a>Klasör içeriği

*Yayımla* klasörü bir veya daha fazla uygulama derleme dosyası, bağımlılık ve isteğe bağlı olarak .NET çalışma zamanı içerir.

.NET Core uygulaması, *kendi içinde* veya *çerçeveye bağımlı dağıtım*olarak yayımlanabilir. Uygulama kendi kendine dahil ise, .NET çalışma zamanını içeren derleme dosyaları *Yayımla* klasörüne dahil edilir. Uygulama çerçeveye bağımlıysa, .NET çalışma zamanı dosyaları dahil değildir çünkü uygulamanın, sunucuda yüklü bir .NET sürümüne başvurusu vardır. Varsayılan dağıtım modeli çerçeveye bağımlıdır. Daha fazla bilgi için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).

*. Exe* ve *. dll* dosyalarına ek olarak, bir ASP.NET Core uygulamasının *Yayımla* klasörü genellikle yapılandırma dosyalarını, statik varlıkları ve MVC görünümlerini içerir. Daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>İşlem Yöneticisi ayarlama

ASP.NET Core uygulaması, bir sunucu önyüklendiğinde ve kilitlenirse yeniden başlatıldığında başlatılması gereken bir konsol uygulamasıdır. Otomatik hale getirmek ve yeniden başlatmaları otomatikleştirmek için bir işlem Yöneticisi gereklidir. ASP.NET Core için en yaygın işlem yöneticileri şunlardır:

* Linux
  * [NGINX](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows hizmeti](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Ters proxy ayarlama

Uygulama [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu kullanıyorsa, [NGINX](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)veya [IIS](xref:host-and-deploy/iis/index) bir ters proxy sunucusu olarak kullanılabilir. Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.

&mdash;Ters ara sunucu sunucusu olan veya olmayan yapılandırma &mdash; , desteklenen bir barındırma yapılandırması. Daha fazla bilgi için bkz. [Kestrel to Use a ters proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Ek yapılandırma olmadan, bir uygulamanın, bir isteğin kaynaklandığı uzak IP adresine (HTTP/HTTPS) ve bu şemaya erişimi olmayabilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Dağıtımları otomatik hale getirmek için Visual Studio ve MSBuild 'i kullanma

Dağıtım genellikle çıktıyı [DotNet Publish](/dotnet/core/tools/dotnet-publish) bir sunucuya kopyalamanın yanı sıra ek görevler gerektirir. Örneğin, daha fazla dosya gerekli olabilir veya *Yayımla* klasöründen dışlanamaz. Visual Studio Web dağıtımı için MSBuild 'i kullanır ve MSBuild, dağıtım sırasında birçok diğer görevi yapmak için özelleştirilebilir. Daha fazla bilgi için bkz <xref:host-and-deploy/visual-studio-publish-profiles> . ve [MSBuild ve Team Foundation Yapı kitabı kullanma](http://msbuildbook.com/) .

Web 'i [Yayımla özelliğini](xref:tutorials/publish-to-azure-webapp-using-vs) veya [yerleşik git desteğini](xref:host-and-deploy/azure-apps/azure-continuous-deployment)kullanarak, uygulamalar doğrudan Visual Studio 'dan Azure App Service dağıtılabilir. Azure DevOps Services [Azure App Service için sürekli dağıtımı](/azure/devops/pipelines/targets/webapp)destekler. Daha fazla bilgi için bkz. [ASP.NET Core ve Azure Ile DevOps](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Azure’da Yayımlama

<xref:tutorials/publish-to-azure-webapp-using-vs>Visual Studio kullanarak Azure 'da uygulama yayımlama hakkında yönergeler için bkz.. [Azure 'da bir ASP.NET Core Web uygulaması](/azure/app-service/app-service-web-get-started-dotnet)oluşturarak ek bir örnek sağlanır.

## <a name="publish-with-msdeploy-on-windows"></a>Windows 'ta MSDeploy ile Yayımla

Bir <xref:host-and-deploy/visual-studio-publish-profiles> Windows komut isteminden [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutunu kullanarak bir uygulamayı Visual Studio yayımlama profiliyle yayımlama hakkında yönergeler için bkz..

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

*web.config* dosyası tarafından sağlanmış yapılandırma ile Internet INFORMATION SERVICES (IIS) dağıtımları için, altındaki makalelere bakın <xref:host-and-deploy/iis/index> .

## <a name="host-in-a-web-farm"></a>Web grubunda barındırma

Bir Web grubu ortamında uygulamaları ASP.NET Core barındırmak için yapılandırma hakkında bilgi için (örneğin, uygulamanızın ölçeklenebilirlik için birden çok örneğinin dağıtılması), bkz <xref:host-and-deploy/web-farm> ..

## <a name="host-on-docker"></a>Docker 'da barındırma

Daha fazla bilgi için bkz. <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* [ASP.NET barındırma](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
