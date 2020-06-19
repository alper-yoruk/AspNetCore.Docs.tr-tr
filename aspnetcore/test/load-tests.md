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
ms.openlocfilehash: 0ec69ad783a4e545ea95ddcb928d03ba6a2e0050
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074384"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="cee35-103">Yük/stres testini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cee35-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="cee35-104">Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="cee35-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="cee35-105">Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="cee35-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="cee35-106">**Yük testleri**: uygulamanın, yanıt hedefini karşılarken belirli bir senaryo için belirtilen bir kullanıcı yükünü işleyemeyeceğini test edin.</span><span class="sxs-lookup"><span data-stu-id="cee35-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="cee35-107">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="cee35-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="cee35-108">**Stres testleri**: genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulamanın kararlılığını test edin.</span><span class="sxs-lookup"><span data-stu-id="cee35-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="cee35-109">Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="cee35-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="cee35-110">Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler.</span><span class="sxs-lookup"><span data-stu-id="cee35-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="cee35-111">Stres altında, uygulama normal koşullarda çalıştırılmamaları.</span><span class="sxs-lookup"><span data-stu-id="cee35-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="cee35-112">Visual Studio 2019 [, yük testini kullanımdan](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)kaldırmaya yönelik planlar duyuruldu.</span><span class="sxs-lookup"><span data-stu-id="cee35-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="cee35-113">Karşılık gelen Azure DevOps bulut tabanlı yük testi hizmeti kapatıldı.</span><span class="sxs-lookup"><span data-stu-id="cee35-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="cee35-114">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="cee35-114">Third-party tools</span></span>

<span data-ttu-id="cee35-115">Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="cee35-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="cee35-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="cee35-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="cee35-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="cee35-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="cee35-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="cee35-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="cee35-119">k6</span><span class="sxs-lookup"><span data-stu-id="cee35-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="cee35-120">Locust</span><span class="sxs-lookup"><span data-stu-id="cee35-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="cee35-121">Batı rüzgar Web dalgalanma</span><span class="sxs-lookup"><span data-stu-id="cee35-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="cee35-122">Netling</span><span class="sxs-lookup"><span data-stu-id="cee35-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="cee35-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="cee35-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)