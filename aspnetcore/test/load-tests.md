---
title: ASP.NET Çekirdek yük/gerilim testi
author: Jeremy-Meng
description: Core uygulamaları ASP.NET yük testi ve stres testi için birkaç önemli araç ve yaklaşım hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664691"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Çekirdek yük/gerilim testi

Bir web uygulamasının performant ve ölçeklenebilir olduğundan emin olmak için yük testi ve stres testi önemlidir. Genellikle benzer testleri paylaşsalar da hedefleri farklıdır.

**Yük testleri** &ndash; Uygulamanın yanıt hedefini karşılarken belirli bir senaryo için belirli bir kullanıcı yükünü işleyip işleyip kullanamayacağını test edin. Uygulama normal koşullarda çalıştırılır.

**Stres testleri** &ndash; Aşırı koşullar altında çalışırken genellikle uzun süre uygulama kararlılığını test edin. Testler, uygulamanın üzerine ani veya kademeli olarak artan yük olan yüksek kullanıcı yükünü yere yerveya uygulamanın bilgi işlem kaynaklarını sınırlandırMaktadır.

Stres testleri, stres altındaki bir uygulamanın başarısızlıktan kurtulup iyileşmeyebileceğini ve beklenen davranışa zarif bir şekilde geri dönüp dönemeyebileceğini belirler. Stres altında, uygulama normal koşullarda çalıştırılamıyor.

Visual Studio 2019, Visual Studio'nun yük testi özelliklerine sahip son sürümüdür. Gelecekte yük test araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar öneririz. Daha fazla bilgi için [Visual Studio 2019 Yayın Notları'na](/visualstudio/releases/2019/release-notes-v16.0#test-tools)bakın.

## <a name="visual-studio-tools"></a>Visual Studio araçları

Visual Studio, kullanıcıların web performansı ve yükleme testleri oluşturmasına, geliştirmesine ve hata ayıklamasına olanak tanır. Bir web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek kullanılabilir.

Visual Studio 2017'yi kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için [Bkz. Quickstart: Bir yük testi projesi oluşturun.](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)

Yük testleri, Azure DevOps kullanılarak şirket içinde çalışacak veya bulutta çalışacak şekilde yapılandırılabilir.

## <a name="third-party-tools"></a>Üçüncü taraf araçları

Aşağıdaki liste, çeşitli özellik kümeleri ile üçüncü taraf web performans araçları içerir:

* [Apaçi JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Mitralyöz](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [Batı Rüzgar WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

