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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="69260-103">ASP.NET Çekirdek yük/gerilim testi</span><span class="sxs-lookup"><span data-stu-id="69260-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="69260-104">Bir web uygulamasının performant ve ölçeklenebilir olduğundan emin olmak için yük testi ve stres testi önemlidir.</span><span class="sxs-lookup"><span data-stu-id="69260-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="69260-105">Genellikle benzer testleri paylaşsalar da hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="69260-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="69260-106">**Yük testleri** &ndash; Uygulamanın yanıt hedefini karşılarken belirli bir senaryo için belirli bir kullanıcı yükünü işleyip işleyip kullanamayacağını test edin.</span><span class="sxs-lookup"><span data-stu-id="69260-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="69260-107">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="69260-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="69260-108">**Stres testleri** &ndash; Aşırı koşullar altında çalışırken genellikle uzun süre uygulama kararlılığını test edin.</span><span class="sxs-lookup"><span data-stu-id="69260-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="69260-109">Testler, uygulamanın üzerine ani veya kademeli olarak artan yük olan yüksek kullanıcı yükünü yere yerveya uygulamanın bilgi işlem kaynaklarını sınırlandırMaktadır.</span><span class="sxs-lookup"><span data-stu-id="69260-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="69260-110">Stres testleri, stres altındaki bir uygulamanın başarısızlıktan kurtulup iyileşmeyebileceğini ve beklenen davranışa zarif bir şekilde geri dönüp dönemeyebileceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="69260-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="69260-111">Stres altında, uygulama normal koşullarda çalıştırılamıyor.</span><span class="sxs-lookup"><span data-stu-id="69260-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="69260-112">Visual Studio 2019, Visual Studio'nun yük testi özelliklerine sahip son sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="69260-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="69260-113">Gelecekte yük test araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar öneririz.</span><span class="sxs-lookup"><span data-stu-id="69260-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="69260-114">Daha fazla bilgi için [Visual Studio 2019 Yayın Notları'na](/visualstudio/releases/2019/release-notes-v16.0#test-tools)bakın.</span><span class="sxs-lookup"><span data-stu-id="69260-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="69260-115">Visual Studio araçları</span><span class="sxs-lookup"><span data-stu-id="69260-115">Visual Studio tools</span></span>

<span data-ttu-id="69260-116">Visual Studio, kullanıcıların web performansı ve yükleme testleri oluşturmasına, geliştirmesine ve hata ayıklamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="69260-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="69260-117">Bir web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="69260-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="69260-118">Visual Studio 2017'yi kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için [Bkz. Quickstart: Bir yük testi projesi oluşturun.](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)</span><span class="sxs-lookup"><span data-stu-id="69260-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="69260-119">Yük testleri, Azure DevOps kullanılarak şirket içinde çalışacak veya bulutta çalışacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="69260-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="69260-120">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="69260-120">Third-party tools</span></span>

<span data-ttu-id="69260-121">Aşağıdaki liste, çeşitli özellik kümeleri ile üçüncü taraf web performans araçları içerir:</span><span class="sxs-lookup"><span data-stu-id="69260-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="69260-122">Apaçi JMeter</span><span class="sxs-lookup"><span data-stu-id="69260-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="69260-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="69260-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="69260-124">Mitralyöz</span><span class="sxs-lookup"><span data-stu-id="69260-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="69260-125">k6</span><span class="sxs-lookup"><span data-stu-id="69260-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="69260-126">Locust</span><span class="sxs-lookup"><span data-stu-id="69260-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="69260-127">Batı Rüzgar WebSurge</span><span class="sxs-lookup"><span data-stu-id="69260-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="69260-128">Netling</span><span class="sxs-lookup"><span data-stu-id="69260-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="69260-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="69260-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

