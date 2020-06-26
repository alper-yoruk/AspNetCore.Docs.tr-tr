---
title: ASP.NET 4. x ve ASP.NET Core arasında seçim yapın
author: rick-anderson
description: ASP.NET Core vs. ASP.NET 4. x ve aralarında nasıl seçim yapılacağını açıklar.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 73495ea851cdfe5d3588b347e75f0af6adacc02e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404944"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="cf92d-103">ASP.NET 4. x ve ASP.NET Core arasında seçim yapın</span><span class="sxs-lookup"><span data-stu-id="cf92d-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="cf92d-104">ASP.NET Core, ASP.NET 4. x ' in bir tasarlayadır.</span><span class="sxs-lookup"><span data-stu-id="cf92d-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="cf92d-105">Bu makalede aralarındaki farklar listelenir.</span><span class="sxs-lookup"><span data-stu-id="cf92d-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="cf92d-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf92d-106">ASP.NET Core</span></span>

<span data-ttu-id="cf92d-107">ASP.NET Core, Windows, macOS veya Linux 'ta modern, bulut tabanlı Web uygulamaları oluşturmaya yönelik açık kaynaklı, platformlar arası bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="cf92d-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="cf92d-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="cf92d-108">ASP.NET 4.x</span></span>

<span data-ttu-id="cf92d-109">ASP.NET 4. x, Windows 'ta kurumsal düzeyde, sunucu tabanlı Web uygulamaları oluşturmak için gereken hizmetleri sağlayan, yetişkinlere yönelik bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="cf92d-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="cf92d-110">Çerçeve seçimi</span><span class="sxs-lookup"><span data-stu-id="cf92d-110">Framework selection</span></span>

<span data-ttu-id="cf92d-111">Aşağıdaki tabloda ASP.NET Core ASP.NET 4. x ile karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="cf92d-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="cf92d-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf92d-112">ASP.NET Core</span></span> | <span data-ttu-id="cf92d-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="cf92d-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="cf92d-114">Windows, macOS veya Linux için derleme</span><span class="sxs-lookup"><span data-stu-id="cf92d-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="cf92d-115">Windows için derleme</span><span class="sxs-lookup"><span data-stu-id="cf92d-115">Build for Windows</span></span>|
|<span data-ttu-id="cf92d-116">[ Razor Sayfalar](xref:razor-pages/index) , ASP.NET Core 2. x itibariyle bir Web Kullanıcı arabirimi oluşturmak için önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="cf92d-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="cf92d-117">Ayrıca bkz. [MVC](xref:mvc/overview), [Web API 'si](xref:tutorials/first-web-api)ve [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="cf92d-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="cf92d-118">[Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr) , [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), Web [kancaları](/aspnet/webhooks/)veya [Web sayfaları](/aspnet/web-pages) kullanın</span><span class="sxs-lookup"><span data-stu-id="cf92d-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="cf92d-119">Makine başına birden çok sürüm</span><span class="sxs-lookup"><span data-stu-id="cf92d-119">Multiple versions per machine</span></span>|<span data-ttu-id="cf92d-120">Makine başına bir sürüm</span><span class="sxs-lookup"><span data-stu-id="cf92d-120">One version per machine</span></span>|
|<span data-ttu-id="cf92d-121">C# veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/), [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)veya [Visual Studio Code](https://code.visualstudio.com/) ile geliştirme #</span><span class="sxs-lookup"><span data-stu-id="cf92d-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="cf92d-122">C#, VB veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/) ile geliştirme #</span><span class="sxs-lookup"><span data-stu-id="cf92d-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="cf92d-123">ASP.NET 4. x 'ten daha yüksek performans</span><span class="sxs-lookup"><span data-stu-id="cf92d-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="cf92d-124">İyi performans</span><span class="sxs-lookup"><span data-stu-id="cf92d-124">Good performance</span></span>|
|[<span data-ttu-id="cf92d-125">.NET Core çalışma zamanı kullan</span><span class="sxs-lookup"><span data-stu-id="cf92d-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="cf92d-126">.NET Framework çalışma zamanını kullan</span><span class="sxs-lookup"><span data-stu-id="cf92d-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="cf92d-127">.NET Framework üzerinde ASP.NET Core 2. x desteği hakkında bilgi için bkz. [ASP.NET Core .NET Framework Hedefleme](xref:index#target-framework) .</span><span class="sxs-lookup"><span data-stu-id="cf92d-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="cf92d-128">ASP.NET Core senaryolar</span><span class="sxs-lookup"><span data-stu-id="cf92d-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="cf92d-129">Web Siteleri</span><span class="sxs-lookup"><span data-stu-id="cf92d-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="cf92d-130">API'ler</span><span class="sxs-lookup"><span data-stu-id="cf92d-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="cf92d-131">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="cf92d-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="cf92d-132">Azure’a ASP.NET Core uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="cf92d-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="cf92d-133">ASP.NET 4. x senaryoları</span><span class="sxs-lookup"><span data-stu-id="cf92d-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="cf92d-134">Web Siteleri</span><span class="sxs-lookup"><span data-stu-id="cf92d-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="cf92d-135">API'ler</span><span class="sxs-lookup"><span data-stu-id="cf92d-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="cf92d-136">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="cf92d-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="cf92d-137">Azure 'da bir ASP.NET 4. x Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf92d-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="cf92d-138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cf92d-138">Additional resources</span></span>

* [<span data-ttu-id="cf92d-139">ASP.NET’e Giriş</span><span class="sxs-lookup"><span data-stu-id="cf92d-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="cf92d-140">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="cf92d-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
