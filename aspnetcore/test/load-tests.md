---
title: Yük/stres testini ASP.NET Core
author: Jeremy-Meng
description: Uygulamalar ASP.NET Core yük testi ve stres testi için birkaç önemli araç ve yaklaşım hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106812"
---
# <a name="aspnet-core-loadstress-testing"></a>Yük/stres testini ASP.NET Core

Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir. Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.

**Yük testleri**: uygulamanın, yanıt hedefini karşılarken belirli bir senaryo için belirtilen bir kullanıcı yükünü işleyemeyeceğini test edin. Uygulama normal koşullarda çalıştırılır.

**Stres testleri**: genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulamanın kararlılığını test edin. Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.

Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler. Stres altında, uygulama normal koşullarda çalıştırılmamaları.

Visual Studio 2019, Visual Studio 'nun yük testi özellikleriyle son sürümüdür. Gelecekte yük testi araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar önerilir. Daha fazla bilgi için bkz. [Visual Studio 2019 sürüm notları](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Visual Studio araçları

Visual Studio, kullanıcıların Web performans ve yük testleri oluşturmalarına, geliştirmesine ve hata ayıklamasına olanak tanır. Bir Web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek mevcuttur.

Visual Studio 2017 kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: yük testi projesi oluşturma](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Yük testleri, şirket içinde çalışacak veya Azure DevOps kullanılarak bulutta çalıştırılacak şekilde yapılandırılabilir.

## <a name="third-party-tools"></a>Üçüncü taraf araçları

Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [Batı rüzgar Web dalgalanma](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

