---
title: ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: Azure'da barındırılan bir ASP.NET Core uygulaması için DevOps ardışık hattı oluşturma konusunda uçuça kılavuz sağlayan bir kılavuz.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658083"
---
# <a name="devops-with-aspnet-core-and-azure"></a>ASP.NET Core ve Azure ile DevOps

[![Kapak Resmi](./media/cover-large.png)](https://aka.ms/devopsbook)

Yazar: [Cam Soper](https://twitter.com/camsoper) ve [Scott Addie](https://twitter.com/scottaddie)

Bu kılavuz indirilebilir PDF [e-kitap](https://aka.ms/devopsbook)olarak kullanılabilir.

## <a name="welcome"></a>Hoş Geldiniz 

.NET için Azure Geliştirme Yaşam Döngüsü kılavuzuna hoş geldiniz! Bu kılavuz, .NET araçlarını ve işlemlerini kullanarak Azure çevresinde bir geliştirme yaşam döngüsü oluşturmanın temel kavramlarını tanıtır. Bu kılavuzu bitirdikten sonra, olgun bir DevOps araç zincirinin avantajlarından yararlanacaksınız.

## <a name="who-this-guide-is-for"></a>Bu kılavuz kimin için

Sen deneyimli bir ASP.NET Core geliştirici (200-300 düzeyi) olmalıdır. Azure hakkında hiçbir şey bilmenizgerekmez, çünkü bu girişte bunu ele alacağız. Bu kılavuz, geliştirmeden çok operasyonlara odaklanmış DevOps mühendisleri için de yararlı olabilir.

Bu kılavuz, Windows geliştiricileri hedefler. Ancak, Linux ve macOS tam olarak .NET Core tarafından desteklenir. Bu kılavuzu Linux/macOS için uyarlamak için, Linux/macOS farklılıkları için bilgi için izleyin.

## <a name="what-this-guide-doesnt-cover"></a>Bu kılavuzun kapsamadığı

Bu kılavuz, .NET geliştiricileri için uçuça sürekli dağıtım deneyimine odaklanmıştır. Azure'daki her şey için kapsamlı bir kılavuz değildir ve Azure hizmetleri için .NET API'lerine kapsamlı bir şekilde odaklanmaz. Vurgu sürekli entegrasyon, dağıtım, izleme ve hata ayıklama etrafında. Kılavuzun sonuna yakın, sonraki adımlar için öneriler sunulur. Önerilere, ASP.NET Çekirdek geliştiricileri için yararlı olan Azure platform hizmetleri dahildir.

## <a name="whats-in-this-guide"></a>Bu kılavuzda neler var?

### <a name="tools-and-downloads"></a>[Araçlar ve indirmeler](xref:azure/devops/tools-and-downloads)

Bu kılavuzda kullanılan araçları nereden edinebileceğinizi öğrenin.

### <a name="deploy-to-app-service"></a>[App Service’e dağıtma](xref:azure/devops/deploy-to-app-service)

Azure Uygulama Hizmeti'ne ASP.NET Core uygulaması dağıtmak için çeşitli yöntemleri öğrenin.

### <a name="continuous-integration-and-deployment"></a>[Sürekli tümleştirme ve dağıtım](xref:azure/devops/cicd)

GitHub, Azure DevOps Hizmetleri ve Azure ile ASP.NET Core uygulamanız için uçtan uca sürekli tümleştirme ve dağıtım çözümü oluşturun.

### <a name="monitor-and-debug"></a>[Monitör ve hata ayıklama](xref:azure/devops/monitor)

Uygulamanızı izlemek, sorun gidermek ve ayarlamak için Azure araçlarını kullanın.

### <a name="next-steps"></a>[Sonraki adımlar](xref:azure/devops/next-steps)

Azure'u öğrenen ASP.NET Core geliştiricisi için diğer öğrenme yolları.

## <a name="additional-introductory-reading"></a>Ek giriş okuması

Bu, bulut bilgi işlem için ilk maruz kalma ise, bu makaleler temel açıklar.

* [Bulut Bilgi İşlem Nedir?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Bulut Bilgi İşlem Örnekleri](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [IaaS nedir?](https://azure.microsoft.com/overview/what-is-iaas/)
* [PaaS nedir?](https://azure.microsoft.com/overview/what-is-paas/)
