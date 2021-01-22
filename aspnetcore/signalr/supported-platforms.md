---
title: SignalRDesteklenen platformları ASP.NET Core
author: bradygaster
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689233"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="69400-103">SignalRDesteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69400-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="69400-104">Sunucu sistem gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="69400-104">Server system requirements</span></span>

<span data-ttu-id="69400-105">SignalR ASP.NET Core için ASP.NET Core desteklediği tüm sunucu platformunu destekler.</span><span class="sxs-lookup"><span data-stu-id="69400-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="69400-106">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="69400-106">JavaScript client</span></span>

<span data-ttu-id="69400-107">[JavaScript Istemcisi](xref:signalr/javascript-client) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:</span><span class="sxs-lookup"><span data-stu-id="69400-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="69400-108">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="69400-108">Browser</span></span>                          | <span data-ttu-id="69400-109">Sürüm</span><span class="sxs-lookup"><span data-stu-id="69400-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="69400-110">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="69400-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="69400-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="69400-111">Current&dagger;</span></span> |
| <span data-ttu-id="69400-112">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="69400-112">Google Chrome, including Android</span></span> | <span data-ttu-id="69400-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="69400-113">Current&dagger;</span></span> |
| <span data-ttu-id="69400-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="69400-114">Microsoft Edge</span></span>                   | <span data-ttu-id="69400-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="69400-115">Current&dagger;</span></span> |
| <span data-ttu-id="69400-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="69400-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="69400-117">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="69400-117">Current&dagger;</span></span> |

<span data-ttu-id="69400-118">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="69400-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="69400-119">JavaScript istemcisi Internet Explorer 'ı ve diğer eski tarayıcıları desteklemez.</span><span class="sxs-lookup"><span data-stu-id="69400-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="69400-120">İstemci desteklenmeyen tarayıcılarda beklenmedik davranış ve hatalara sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="69400-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="69400-121">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="69400-121">.NET client</span></span>

<span data-ttu-id="69400-122">[.Net Client](xref:signalr/dotnet-client) , ASP.NET Core tarafından desteklenen herhangi bir platformda çalışır.</span><span class="sxs-lookup"><span data-stu-id="69400-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="69400-123">Örneğin, [Xamarin geliştiricileri SignalR ](https://github.com/aspnet/Announcements/issues/305) Xamarin. iOS 8.4.0.1 ve üzeri ve Android uygulamalarını kullanarak Xamarin. iOS 11.14.0.4 ve üstünü kullanarak Android uygulamaları oluşturmak için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="69400-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="69400-124">Sunucu IIS çalıştırıyorsa, WebSockets aktarımı Windows Server 2012 veya üzeri sürümlerde IIS 8,0 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="69400-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="69400-125">Diğer aktarımlar tüm platformlarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="69400-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="69400-126">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="69400-126">Java client</span></span>

<span data-ttu-id="69400-127">[Java istemcisi](xref:signalr/java-client) , Java 8 ve sonraki sürümlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="69400-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="69400-128">Desteklenmeyen istemciler</span><span class="sxs-lookup"><span data-stu-id="69400-128">Unsupported clients</span></span>

<span data-ttu-id="69400-129">Aşağıdaki istemciler mevcuttur, ancak deneysel veya resmi olmayan bir.</span><span class="sxs-lookup"><span data-stu-id="69400-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="69400-130">Şu anda desteklenmemektedir ve hiçbir şekilde bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="69400-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="69400-131">[C++ istemcisi](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="69400-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="69400-132">[Swift istemcisi](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="69400-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
