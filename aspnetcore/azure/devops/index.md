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
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="bcc9a-103">ASP.NET Core ve Azure ile DevOps</span><span class="sxs-lookup"><span data-stu-id="bcc9a-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="bcc9a-104">[![Kapak Resmi](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="bcc9a-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="bcc9a-105">Yazar: [Cam Soper](https://twitter.com/camsoper) ve [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="bcc9a-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="bcc9a-106">Bu kılavuz indirilebilir PDF [e-kitap](https://aka.ms/devopsbook)olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="bcc9a-107">Hoş Geldiniz</span><span class="sxs-lookup"><span data-stu-id="bcc9a-107">Welcome</span></span> 

<span data-ttu-id="bcc9a-108">.NET için Azure Geliştirme Yaşam Döngüsü kılavuzuna hoş geldiniz!</span><span class="sxs-lookup"><span data-stu-id="bcc9a-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="bcc9a-109">Bu kılavuz, .NET araçlarını ve işlemlerini kullanarak Azure çevresinde bir geliştirme yaşam döngüsü oluşturmanın temel kavramlarını tanıtır.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="bcc9a-110">Bu kılavuzu bitirdikten sonra, olgun bir DevOps araç zincirinin avantajlarından yararlanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="bcc9a-111">Bu kılavuz kimin için</span><span class="sxs-lookup"><span data-stu-id="bcc9a-111">Who this guide is for</span></span>

<span data-ttu-id="bcc9a-112">Sen deneyimli bir ASP.NET Core geliştirici (200-300 düzeyi) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="bcc9a-113">Azure hakkında hiçbir şey bilmenizgerekmez, çünkü bu girişte bunu ele alacağız.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="bcc9a-114">Bu kılavuz, geliştirmeden çok operasyonlara odaklanmış DevOps mühendisleri için de yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="bcc9a-115">Bu kılavuz, Windows geliştiricileri hedefler.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-115">This guide targets Windows developers.</span></span> <span data-ttu-id="bcc9a-116">Ancak, Linux ve macOS tam olarak .NET Core tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="bcc9a-117">Bu kılavuzu Linux/macOS için uyarlamak için, Linux/macOS farklılıkları için bilgi için izleyin.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="bcc9a-118">Bu kılavuzun kapsamadığı</span><span class="sxs-lookup"><span data-stu-id="bcc9a-118">What this guide doesn't cover</span></span>

<span data-ttu-id="bcc9a-119">Bu kılavuz, .NET geliştiricileri için uçuça sürekli dağıtım deneyimine odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="bcc9a-120">Azure'daki her şey için kapsamlı bir kılavuz değildir ve Azure hizmetleri için .NET API'lerine kapsamlı bir şekilde odaklanmaz.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="bcc9a-121">Vurgu sürekli entegrasyon, dağıtım, izleme ve hata ayıklama etrafında.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="bcc9a-122">Kılavuzun sonuna yakın, sonraki adımlar için öneriler sunulur.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="bcc9a-123">Önerilere, ASP.NET Çekirdek geliştiricileri için yararlı olan Azure platform hizmetleri dahildir.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="bcc9a-124">Bu kılavuzda neler var?</span><span class="sxs-lookup"><span data-stu-id="bcc9a-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="bcc9a-125">Araçlar ve indirmeler</span><span class="sxs-lookup"><span data-stu-id="bcc9a-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="bcc9a-126">Bu kılavuzda kullanılan araçları nereden edinebileceğinizi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="bcc9a-127">App Service’e dağıtma</span><span class="sxs-lookup"><span data-stu-id="bcc9a-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="bcc9a-128">Azure Uygulama Hizmeti'ne ASP.NET Core uygulaması dağıtmak için çeşitli yöntemleri öğrenin.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="bcc9a-129">Sürekli tümleştirme ve dağıtım</span><span class="sxs-lookup"><span data-stu-id="bcc9a-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="bcc9a-130">GitHub, Azure DevOps Hizmetleri ve Azure ile ASP.NET Core uygulamanız için uçtan uca sürekli tümleştirme ve dağıtım çözümü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="bcc9a-131">Monitör ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="bcc9a-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="bcc9a-132">Uygulamanızı izlemek, sorun gidermek ve ayarlamak için Azure araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="bcc9a-133">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="bcc9a-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="bcc9a-134">Azure'u öğrenen ASP.NET Core geliştiricisi için diğer öğrenme yolları.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="bcc9a-135">Ek giriş okuması</span><span class="sxs-lookup"><span data-stu-id="bcc9a-135">Additional introductory reading</span></span>

<span data-ttu-id="bcc9a-136">Bu, bulut bilgi işlem için ilk maruz kalma ise, bu makaleler temel açıklar.</span><span class="sxs-lookup"><span data-stu-id="bcc9a-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="bcc9a-137">Bulut Bilgi İşlem Nedir?</span><span class="sxs-lookup"><span data-stu-id="bcc9a-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="bcc9a-138">Bulut Bilgi İşlem Örnekleri</span><span class="sxs-lookup"><span data-stu-id="bcc9a-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="bcc9a-139">IaaS nedir?</span><span class="sxs-lookup"><span data-stu-id="bcc9a-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="bcc9a-140">PaaS nedir?</span><span class="sxs-lookup"><span data-stu-id="bcc9a-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
