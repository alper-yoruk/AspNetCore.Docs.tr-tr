---
title: :::no-loc(SignalR):::Desteklenen platformları ASP.NET Core
author: bradygaster
description: 'ASP.NET Core için desteklenen platformlar hakkında bilgi edinin :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/supported-platforms
ms.openlocfilehash: 761edbbe7bab28d2340207a0adea0718b37c7ec1
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690342"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="71cd7-103">:::no-loc(SignalR):::Desteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71cd7-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="71cd7-104">Sunucu sistem gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="71cd7-104">Server system requirements</span></span>

<span data-ttu-id="71cd7-105">:::no-loc(SignalR)::: ASP.NET Core için ASP.NET Core desteklediği tüm sunucu platformunu destekler.</span><span class="sxs-lookup"><span data-stu-id="71cd7-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="71cd7-106">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="71cd7-106">JavaScript client</span></span>

<span data-ttu-id="71cd7-107">[JavaScript Istemcisi](xref:signalr/javascript-client) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:</span><span class="sxs-lookup"><span data-stu-id="71cd7-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="71cd7-108">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="71cd7-108">Browser</span></span>                          | <span data-ttu-id="71cd7-109">Sürüm</span><span class="sxs-lookup"><span data-stu-id="71cd7-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="71cd7-110">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="71cd7-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="71cd7-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="71cd7-111">Current&dagger;</span></span> |
| <span data-ttu-id="71cd7-112">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="71cd7-112">Google Chrome, including Android</span></span> | <span data-ttu-id="71cd7-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="71cd7-113">Current&dagger;</span></span> |
| <span data-ttu-id="71cd7-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="71cd7-114">Microsoft Edge</span></span>                   | <span data-ttu-id="71cd7-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="71cd7-115">Current&dagger;</span></span> |
| <span data-ttu-id="71cd7-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="71cd7-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="71cd7-117">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="71cd7-117">Current&dagger;</span></span> |

<span data-ttu-id="71cd7-118">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="71cd7-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="71cd7-119">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="71cd7-119">.NET client</span></span>

<span data-ttu-id="71cd7-120">[.Net Client](xref:signalr/dotnet-client) , ASP.NET Core tarafından desteklenen herhangi bir platformda çalışır.</span><span class="sxs-lookup"><span data-stu-id="71cd7-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="71cd7-121">Örneğin, [Xamarin geliştiricileri :::no-loc(SignalR)::: ](https://github.com/aspnet/Announcements/issues/305) Xamarin. iOS 8.4.0.1 ve üzeri ve Android uygulamalarını kullanarak Xamarin. iOS 11.14.0.4 ve üstünü kullanarak Android uygulamaları oluşturmak için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="71cd7-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="71cd7-122">Sunucu IIS çalıştırıyorsa, WebSockets aktarımı Windows Server 2012 veya üzeri sürümlerde IIS 8,0 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="71cd7-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="71cd7-123">Diğer aktarımlar tüm platformlarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="71cd7-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="71cd7-124">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="71cd7-124">Java client</span></span>

<span data-ttu-id="71cd7-125">[Java istemcisi](xref:signalr/java-client) , Java 8 ve sonraki sürümlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="71cd7-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="71cd7-126">Desteklenmeyen istemciler</span><span class="sxs-lookup"><span data-stu-id="71cd7-126">Unsupported clients</span></span>

<span data-ttu-id="71cd7-127">Aşağıdaki istemciler mevcuttur, ancak deneysel veya resmi olmayan bir.</span><span class="sxs-lookup"><span data-stu-id="71cd7-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="71cd7-128">Şu anda desteklenmemektedir ve hiçbir şekilde bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="71cd7-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="71cd7-129">[C++ istemcisi](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="71cd7-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="71cd7-130">[Swift istemcisi](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="71cd7-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
