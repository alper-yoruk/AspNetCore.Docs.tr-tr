---
title: 4.x ile ASP.NET 4.x arasında seçim yapma ve ASP.NET Core
author: rick-anderson
description: Core ve ASP.NET 4.x ASP.NET ve aralarında nasıl seçim yapılacağını açıklar.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665244"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="2c0d7-103">4.x ile ASP.NET 4.x arasında seçim yapma ve ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c0d7-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="2c0d7-104">ASP.NET Core, ASP.NET 4.x'in yeniden tasarlanmasıdır.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="2c0d7-105">Bu makalede, aralarındaki farklar listeleneb.)</span><span class="sxs-lookup"><span data-stu-id="2c0d7-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="2c0d7-106">ASP.NET Çekirdeği</span><span class="sxs-lookup"><span data-stu-id="2c0d7-106">ASP.NET Core</span></span>

<span data-ttu-id="2c0d7-107">ASP.NET Core, Windows, macOS veya Linux'ta modern, bulut tabanlı web uygulamaları oluşturmak için açık kaynak kodlu, çapraz platform çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="2c0d7-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="2c0d7-108">ASP.NET 4.x</span></span>

<span data-ttu-id="2c0d7-109">ASP.NET 4.x, Windows'da kurumsal sınıf, sunucu tabanlı web uygulamaları oluşturmak için gereken hizmetleri sağlayan olgun bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="2c0d7-110">Çerçeve seçimi</span><span class="sxs-lookup"><span data-stu-id="2c0d7-110">Framework selection</span></span>

<span data-ttu-id="2c0d7-111">Aşağıdaki tablo, ASP.NET Core ile ASP.NET 4.x'i karşılaştırır.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="2c0d7-112">ASP.NET Çekirdeği</span><span class="sxs-lookup"><span data-stu-id="2c0d7-112">ASP.NET Core</span></span> | <span data-ttu-id="2c0d7-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="2c0d7-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="2c0d7-114">Windows, macOS veya Linux için yapı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="2c0d7-115">Windows için yapı</span><span class="sxs-lookup"><span data-stu-id="2c0d7-115">Build for Windows</span></span>|
|<span data-ttu-id="2c0d7-116">[Razor Pages,](xref:razor-pages/index) Core 2.x'ASP.NET itibaren bir Web Web UI oluşturmak için önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="2c0d7-117">Ayrıca bakınız [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction) [Web API](xref:tutorials/first-web-api), ve .</span><span class="sxs-lookup"><span data-stu-id="2c0d7-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="2c0d7-118">[Web Formlarını,](/aspnet/web-forms) [SignalR](/aspnet/signalr) [MVC'yi,](/aspnet/mvc) [Web API'yi,](/aspnet/web-api/) [Web Hooks'u](/aspnet/webhooks/)veya [Web Sayfalarını](/aspnet/web-pages) kullanma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="2c0d7-119">Makine başına birden fazla versiyon</span><span class="sxs-lookup"><span data-stu-id="2c0d7-119">Multiple versions per machine</span></span>|<span data-ttu-id="2c0d7-120">Makine başına bir sürüm</span><span class="sxs-lookup"><span data-stu-id="2c0d7-120">One version per machine</span></span>|
|<span data-ttu-id="2c0d7-121">Visual [Studio](https://visualstudio.microsoft.com/vs/), [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)veya C# veya F kullanarak Visual Studio [Kodu](https://code.visualstudio.com/) ile geliştirin #</span><span class="sxs-lookup"><span data-stu-id="2c0d7-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="2c0d7-122">C#, VB veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/) ile geliştirin #</span><span class="sxs-lookup"><span data-stu-id="2c0d7-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="2c0d7-123">ASP.NET 4.x'ten daha yüksek performans</span><span class="sxs-lookup"><span data-stu-id="2c0d7-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="2c0d7-124">İyi performans</span><span class="sxs-lookup"><span data-stu-id="2c0d7-124">Good performance</span></span>|
|[<span data-ttu-id="2c0d7-125">.NET Core çalışma süresini kullanma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="2c0d7-126">.NET Framework çalışma süresini kullanma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="2c0d7-127">ASP.NET Core 2.x desteği hakkında bilgi için [ASP.NET .NET Framework](xref:index#target-framework) hedeflemesi.</span><span class="sxs-lookup"><span data-stu-id="2c0d7-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="2c0d7-128">ASP.NET Temel senaryoları</span><span class="sxs-lookup"><span data-stu-id="2c0d7-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="2c0d7-129">Web Siteleri</span><span class="sxs-lookup"><span data-stu-id="2c0d7-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="2c0d7-130">API'ler</span><span class="sxs-lookup"><span data-stu-id="2c0d7-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="2c0d7-131">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="2c0d7-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="2c0d7-132">Azure’a ASP.NET Core uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="2c0d7-133">ASP.NET 4.x senaryoları</span><span class="sxs-lookup"><span data-stu-id="2c0d7-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="2c0d7-134">Web Siteleri</span><span class="sxs-lookup"><span data-stu-id="2c0d7-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="2c0d7-135">API'ler</span><span class="sxs-lookup"><span data-stu-id="2c0d7-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="2c0d7-136">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="2c0d7-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="2c0d7-137">Azure'da ASP.NET 4.x web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="2c0d7-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="2c0d7-138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c0d7-138">Additional resources</span></span>

* [<span data-ttu-id="2c0d7-139">ASP.NET’e Giriş</span><span class="sxs-lookup"><span data-stu-id="2c0d7-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="2c0d7-140">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="2c0d7-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
