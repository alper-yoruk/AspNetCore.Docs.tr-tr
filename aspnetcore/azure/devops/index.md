---
title: ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: Azure 'da barındırılan bir ASP.NET Core uygulaması için DevOps işlem hattı oluşturmaya yönelik uçtan uca rehberlik sağlayan bir kılavuz.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/index
ms.openlocfilehash: 10a6bae73743d3063ad2e1ab49fc418ad1d63756
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052298"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="e46c7-103">ASP.NET Core ve Azure ile DevOps</span><span class="sxs-lookup"><span data-stu-id="e46c7-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="e46c7-104">[![Kapak resmi](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="e46c7-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="e46c7-105">By [Cam Soper](https://twitter.com/camsoper) ve [Scott Ade](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e46c7-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="e46c7-106">Bu kılavuz, [indirilebilir BIR PDF e-kitabı](https://aka.ms/devopsbook)olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e46c7-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="e46c7-107">Hoş Geldiniz</span><span class="sxs-lookup"><span data-stu-id="e46c7-107">Welcome</span></span> 

<span data-ttu-id="e46c7-108">.NET için Azure geliştirme yaşam döngüsü kılavuzuna hoş geldiniz!</span><span class="sxs-lookup"><span data-stu-id="e46c7-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="e46c7-109">Bu kılavuzda, .NET araçları ve süreçleri kullanılarak Azure 'da bir geliştirme yaşam döngüsü oluşturmaya yönelik temel kavramlar tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e46c7-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="e46c7-110">Bu kılavuzu tamamladıktan sonra, yetişkinlere yönelik bir DevOps araç zincirinin avantajlarından yararlanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e46c7-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="e46c7-111">Bu kılavuzun kim olduğunu</span><span class="sxs-lookup"><span data-stu-id="e46c7-111">Who this guide is for</span></span>

<span data-ttu-id="e46c7-112">Deneyimli bir ASP.NET Core Geliştirici (200-300 düzeyi) olmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e46c7-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="e46c7-113">Bu giriş bölümünde yer alan bilgileri sunduğumuz için Azure ile ilgili herhangi bir şey bilmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e46c7-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="e46c7-114">Bu kılavuz, geliştirmeden işlemlere daha fazla odaklanan DevOps mühendisleri için de kullanışlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="e46c7-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="e46c7-115">Bu kılavuz Windows geliştiricilerini hedefler.</span><span class="sxs-lookup"><span data-stu-id="e46c7-115">This guide targets Windows developers.</span></span> <span data-ttu-id="e46c7-116">Ancak, Linux ve macOS .NET Core tarafından tam olarak desteklenmektedir.</span><span class="sxs-lookup"><span data-stu-id="e46c7-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="e46c7-117">Linux/macOS için bu kılavuzu uyarlamanız için, Linux/macOS farklılıkları için belirtme çizgilerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="e46c7-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="e46c7-118">Bu kılavuzun kapsamıyor</span><span class="sxs-lookup"><span data-stu-id="e46c7-118">What this guide doesn't cover</span></span>

<span data-ttu-id="e46c7-119">Bu kılavuz, .NET geliştiricileri için uçtan uca bir sürekli dağıtım deneyimine odaklanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="e46c7-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="e46c7-120">Azure 'a yönelik kapsamlı bir kılavuz değildir ve Azure hizmetleri için .NET API 'Lerinde kapsamlı bir şekilde odaklanmaz.</span><span class="sxs-lookup"><span data-stu-id="e46c7-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="e46c7-121">Vurgu, sürekli tümleştirme, dağıtım, izleme ve hata ayıklama etrafında bulunur.</span><span class="sxs-lookup"><span data-stu-id="e46c7-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="e46c7-122">Kılavuzun sonuna yakın bir şekilde, sonraki adımlara yönelik öneriler sunulur.</span><span class="sxs-lookup"><span data-stu-id="e46c7-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="e46c7-123">Önerilere dahil olan Azure platform hizmetleri, ASP.NET Core geliştiricileri için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e46c7-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="e46c7-124">Bu kılavuzda neler vardır</span><span class="sxs-lookup"><span data-stu-id="e46c7-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="e46c7-125">Araçlar ve indirmeler</span><span class="sxs-lookup"><span data-stu-id="e46c7-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="e46c7-126">Bu kılavuzda kullanılan araçların nereden edineceğinizi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="e46c7-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="e46c7-127">App Service’e dağıtma</span><span class="sxs-lookup"><span data-stu-id="e46c7-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="e46c7-128">Azure App Service ASP.NET Core uygulama dağıtmaya yönelik çeşitli yöntemler hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="e46c7-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="e46c7-129">Sürekli tümleştirme ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="e46c7-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="e46c7-130">GitHub, Azure DevOps Services ve Azure ile ASP.NET Core uygulamanız için uçtan uca bir sürekli tümleştirme ve dağıtım çözümü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e46c7-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="e46c7-131">İzleme ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="e46c7-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="e46c7-132">Uygulamanızı izlemek, sorunlarını gidermek ve ayarlamak için Azure araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="e46c7-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="e46c7-133">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="e46c7-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="e46c7-134">ASP.NET Core geliştirici için diğer öğrenme yolları Azure 'ı öğreniyor.</span><span class="sxs-lookup"><span data-stu-id="e46c7-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="e46c7-135">Ek tanıtım okuma</span><span class="sxs-lookup"><span data-stu-id="e46c7-135">Additional introductory reading</span></span>

<span data-ttu-id="e46c7-136">Bu, bulut bilgi işlemin ilk etkilenmesi ise, bu makalelerde temel bilgiler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e46c7-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="e46c7-137">Bulut bilgi Işlem nedir?</span><span class="sxs-lookup"><span data-stu-id="e46c7-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="e46c7-138">Bulut bilgi Işlem örnekleri</span><span class="sxs-lookup"><span data-stu-id="e46c7-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="e46c7-139">IaaS Nedir?</span><span class="sxs-lookup"><span data-stu-id="e46c7-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="e46c7-140">PaaS nedir?</span><span class="sxs-lookup"><span data-stu-id="e46c7-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
