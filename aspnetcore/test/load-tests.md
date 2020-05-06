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
ms.openlocfilehash: cf99eaa71846ea705a312b0fb773605fc77b0d97
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775264"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="5c06d-103">Yük/stres testini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c06d-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="5c06d-104">Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="5c06d-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="5c06d-105">Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="5c06d-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="5c06d-106">**Yük testleri** &ndash; , uygulamanın belirli bir kullanıcı yükünü, yanıt hedefini karşılarken, belirli bir senaryo için işleyebilip işleyemeyeceğini test eder.</span><span class="sxs-lookup"><span data-stu-id="5c06d-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="5c06d-107">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5c06d-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="5c06d-108">**Stres testleri** &ndash; , genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulama kararlılığını test eder.</span><span class="sxs-lookup"><span data-stu-id="5c06d-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="5c06d-109">Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="5c06d-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="5c06d-110">Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler.</span><span class="sxs-lookup"><span data-stu-id="5c06d-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="5c06d-111">Stres altında, uygulama normal koşullarda çalıştırılmamaları.</span><span class="sxs-lookup"><span data-stu-id="5c06d-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="5c06d-112">Visual Studio 2019, Visual Studio 'nun yük testi özellikleriyle son sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="5c06d-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="5c06d-113">Gelecekte yük testi araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar önerilir.</span><span class="sxs-lookup"><span data-stu-id="5c06d-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="5c06d-114">Daha fazla bilgi için bkz. [Visual Studio 2019 sürüm notları](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="5c06d-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="5c06d-115">Visual Studio araçları</span><span class="sxs-lookup"><span data-stu-id="5c06d-115">Visual Studio tools</span></span>

<span data-ttu-id="5c06d-116">Visual Studio, kullanıcıların Web performans ve yük testleri oluşturmalarına, geliştirmesine ve hata ayıklamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5c06d-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="5c06d-117">Bir Web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5c06d-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="5c06d-118">Visual Studio 2017 kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: yük testi projesi oluşturma](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="5c06d-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="5c06d-119">Yük testleri, şirket içinde çalışacak veya Azure DevOps kullanılarak bulutta çalıştırılacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c06d-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="5c06d-120">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="5c06d-120">Third-party tools</span></span>

<span data-ttu-id="5c06d-121">Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="5c06d-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="5c06d-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="5c06d-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="5c06d-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="5c06d-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="5c06d-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="5c06d-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="5c06d-125">k6</span><span class="sxs-lookup"><span data-stu-id="5c06d-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="5c06d-126">Locust</span><span class="sxs-lookup"><span data-stu-id="5c06d-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="5c06d-127">Batı rüzgar Web dalgalanma</span><span class="sxs-lookup"><span data-stu-id="5c06d-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="5c06d-128">Netling</span><span class="sxs-lookup"><span data-stu-id="5c06d-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="5c06d-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="5c06d-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

