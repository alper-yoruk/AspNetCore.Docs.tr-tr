---
title: ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: Azure 'da barındırılan bir ASP.NET Core uygulaması için DevOps işlem hattı oluşturmaya yönelik uçtan uca rehberlik sağlayan bir kılavuz.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10df1eab6de064b824ce067aff3ecc07e81c38fd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012273"
---
# <a name="devops-with-aspnet-core-and-azure"></a>ASP.NET Core ve Azure ile DevOps

[![Kapak resmi](./media/cover-large.png)](https://aka.ms/devopsbook)

By [Cam Soper](https://twitter.com/camsoper) ve [Scott Ade](https://twitter.com/scottaddie)

Bu kılavuz, [indirilebilir BIR PDF e-kitabı](https://aka.ms/devopsbook)olarak sunulmaktadır.

## <a name="welcome"></a>Hoş Geldiniz 

.NET için Azure geliştirme yaşam döngüsü kılavuzuna hoş geldiniz! Bu kılavuzda, .NET araçları ve süreçleri kullanılarak Azure 'da bir geliştirme yaşam döngüsü oluşturmaya yönelik temel kavramlar tanıtılmaktadır. Bu kılavuzu tamamladıktan sonra, yetişkinlere yönelik bir DevOps araç zincirinin avantajlarından yararlanabilirsiniz.

## <a name="who-this-guide-is-for"></a>Bu kılavuzun kim olduğunu

Deneyimli bir ASP.NET Core Geliştirici (200-300 düzeyi) olmanız gerekir. Bu giriş bölümünde yer alan bilgileri sunduğumuz için Azure ile ilgili herhangi bir şey bilmeniz gerekmez. Bu kılavuz, geliştirmeden işlemlere daha fazla odaklanan DevOps mühendisleri için de kullanışlı olabilir.

Bu kılavuz Windows geliştiricilerini hedefler. Ancak, Linux ve macOS .NET Core tarafından tam olarak desteklenmektedir. Linux/macOS için bu kılavuzu uyarlamanız için, Linux/macOS farklılıkları için belirtme çizgilerini izleyin.

## <a name="what-this-guide-doesnt-cover"></a>Bu kılavuzun kapsamıyor

Bu kılavuz, .NET geliştiricileri için uçtan uca bir sürekli dağıtım deneyimine odaklanılmıştır. Azure 'a yönelik kapsamlı bir kılavuz değildir ve Azure hizmetleri için .NET API 'Lerinde kapsamlı bir şekilde odaklanmaz. Vurgu, sürekli tümleştirme, dağıtım, izleme ve hata ayıklama etrafında bulunur. Kılavuzun sonuna yakın bir şekilde, sonraki adımlara yönelik öneriler sunulur. Önerilere dahil olan Azure platform hizmetleri, ASP.NET Core geliştiricileri için yararlıdır.

## <a name="whats-in-this-guide"></a>Bu kılavuzda neler vardır

### <a name="tools-and-downloads"></a>[Araçlar ve indirmeler](xref:azure/devops/tools-and-downloads)

Bu kılavuzda kullanılan araçların nereden edineceğinizi öğrenin.

### <a name="deploy-to-app-service"></a>[App Service’e dağıtma](xref:azure/devops/deploy-to-app-service)

Azure App Service ASP.NET Core uygulama dağıtmaya yönelik çeşitli yöntemler hakkında bilgi edinin.

### <a name="continuous-integration-and-deployment"></a>[Sürekli tümleştirme ve dağıtım](xref:azure/devops/cicd)

GitHub, Azure DevOps Services ve Azure ile ASP.NET Core uygulamanız için uçtan uca bir sürekli tümleştirme ve dağıtım çözümü oluşturun.

### <a name="monitor-and-debug"></a>[İzleme ve hata ayıklama](xref:azure/devops/monitor)

Uygulamanızı izlemek, sorunlarını gidermek ve ayarlamak için Azure araçlarını kullanın.

### <a name="next-steps"></a>[Sonraki adımlar](xref:azure/devops/next-steps)

ASP.NET Core geliştirici için diğer öğrenme yolları Azure 'ı öğreniyor.

## <a name="additional-introductory-reading"></a>Ek tanıtım okuma

Bu, bulut bilgi işlemin ilk etkilenmesi ise, bu makalelerde temel bilgiler açıklanmaktadır.

* [Bulut bilgi Işlem nedir?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Bulut bilgi Işlem örnekleri](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [IaaS Nedir?](https://azure.microsoft.com/overview/what-is-iaas/)
* [PaaS nedir?](https://azure.microsoft.com/overview/what-is-paas/)
