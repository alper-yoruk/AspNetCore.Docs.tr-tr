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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="4b126-103">Yük/stres testini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b126-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="4b126-104">Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="4b126-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="4b126-105">Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="4b126-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="4b126-106">**Yük testleri**: uygulamanın, yanıt hedefini karşılarken belirli bir senaryo için belirtilen bir kullanıcı yükünü işleyemeyeceğini test edin.</span><span class="sxs-lookup"><span data-stu-id="4b126-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="4b126-107">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4b126-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="4b126-108">**Stres testleri**: genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulamanın kararlılığını test edin.</span><span class="sxs-lookup"><span data-stu-id="4b126-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="4b126-109">Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="4b126-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="4b126-110">Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler.</span><span class="sxs-lookup"><span data-stu-id="4b126-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="4b126-111">Stres altında, uygulama normal koşullarda çalıştırılmamaları.</span><span class="sxs-lookup"><span data-stu-id="4b126-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="4b126-112">Visual Studio 2019, Visual Studio 'nun yük testi özellikleriyle son sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="4b126-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="4b126-113">Gelecekte yük testi araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar önerilir.</span><span class="sxs-lookup"><span data-stu-id="4b126-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="4b126-114">Daha fazla bilgi için bkz. [Visual Studio 2019 sürüm notları](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="4b126-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="4b126-115">Visual Studio araçları</span><span class="sxs-lookup"><span data-stu-id="4b126-115">Visual Studio tools</span></span>

<span data-ttu-id="4b126-116">Visual Studio, kullanıcıların Web performans ve yük testleri oluşturmalarına, geliştirmesine ve hata ayıklamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="4b126-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="4b126-117">Bir Web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="4b126-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="4b126-118">Visual Studio 2017 kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: yük testi projesi oluşturma](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="4b126-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="4b126-119">Yük testleri, şirket içinde çalışacak veya Azure DevOps kullanılarak bulutta çalıştırılacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4b126-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="4b126-120">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="4b126-120">Third-party tools</span></span>

<span data-ttu-id="4b126-121">Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="4b126-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="4b126-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="4b126-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="4b126-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="4b126-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="4b126-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="4b126-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="4b126-125">k6</span><span class="sxs-lookup"><span data-stu-id="4b126-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="4b126-126">Locust</span><span class="sxs-lookup"><span data-stu-id="4b126-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="4b126-127">Batı rüzgar Web dalgalanma</span><span class="sxs-lookup"><span data-stu-id="4b126-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="4b126-128">Netling</span><span class="sxs-lookup"><span data-stu-id="4b126-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="4b126-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="4b126-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

