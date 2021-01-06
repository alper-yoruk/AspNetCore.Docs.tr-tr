---
title: Yük/stres testini ASP.NET Core
author: Jeremy-Meng
description: Uygulamalar ASP.NET Core yük testi ve stres testi için birkaç önemli araç ve yaklaşım hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060722"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="4f92c-103">Yük/stres testini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f92c-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="4f92c-104">Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="4f92c-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="4f92c-105">Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="4f92c-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="4f92c-106">**Yük testleri**: uygulamanın, yanıt hedefini karşılarken belirli bir senaryo için belirtilen bir kullanıcı yükünü işleyemeyeceğini test edin.</span><span class="sxs-lookup"><span data-stu-id="4f92c-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="4f92c-107">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4f92c-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="4f92c-108">**Stres testleri**: genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulamanın kararlılığını test edin.</span><span class="sxs-lookup"><span data-stu-id="4f92c-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="4f92c-109">Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="4f92c-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="4f92c-110">Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler.</span><span class="sxs-lookup"><span data-stu-id="4f92c-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="4f92c-111">Stres altında, uygulama normal koşullarda çalıştırılmamaları.</span><span class="sxs-lookup"><span data-stu-id="4f92c-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="4f92c-112">Visual Studio 2019 [, yük testini kullanımdan](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)kaldırmaya yönelik planlar duyuruldu.</span><span class="sxs-lookup"><span data-stu-id="4f92c-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="4f92c-113">Karşılık gelen Azure DevOps bulut tabanlı yük testi hizmeti kapatıldı.</span><span class="sxs-lookup"><span data-stu-id="4f92c-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="4f92c-114">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="4f92c-114">Third-party tools</span></span>

<span data-ttu-id="4f92c-115">Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="4f92c-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="4f92c-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="4f92c-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="4f92c-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="4f92c-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="4f92c-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="4f92c-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="4f92c-119">k6</span><span class="sxs-lookup"><span data-stu-id="4f92c-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="4f92c-120">Locust</span><span class="sxs-lookup"><span data-stu-id="4f92c-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="4f92c-121">Batı rüzgar Web dalgalanma</span><span class="sxs-lookup"><span data-stu-id="4f92c-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="4f92c-122">Netling</span><span class="sxs-lookup"><span data-stu-id="4f92c-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="4f92c-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="4f92c-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)