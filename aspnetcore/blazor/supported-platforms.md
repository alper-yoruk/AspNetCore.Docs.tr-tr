---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054963"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="acb13-103">BlazorDesteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acb13-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="acb13-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="acb13-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="acb13-105">Blazor WebAssembly ve Blazor Server Aşağıdaki tabloda gösterilen tarayıcılarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="acb13-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="acb13-106">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="acb13-106">Browser</span></span>                          | <span data-ttu-id="acb13-107">Sürüm</span><span class="sxs-lookup"><span data-stu-id="acb13-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="acb13-108">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="acb13-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="acb13-109">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-109">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-110">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="acb13-110">Google Chrome, including Android</span></span> | <span data-ttu-id="acb13-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-111">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="acb13-112">Microsoft Edge</span></span>                   | <span data-ttu-id="acb13-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-113">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="acb13-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="acb13-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-115">Current&dagger;</span></span> |  

<span data-ttu-id="acb13-116">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="acb13-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="acb13-117">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="acb13-117">Browser</span></span>                          | <span data-ttu-id="acb13-118">Sürüm</span><span class="sxs-lookup"><span data-stu-id="acb13-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="acb13-119">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="acb13-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="acb13-120">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-120">Current&dagger;</span></span>       |
| <span data-ttu-id="acb13-121">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="acb13-121">Google Chrome, including Android</span></span> | <span data-ttu-id="acb13-122">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-122">Current&dagger;</span></span>       |
| <span data-ttu-id="acb13-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="acb13-123">Microsoft Edge</span></span>                   | <span data-ttu-id="acb13-124">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-124">Current&dagger;</span></span>       |
| <span data-ttu-id="acb13-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="acb13-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="acb13-126">Desteklenmiyor&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="acb13-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="acb13-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="acb13-128">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-128">Current&dagger;</span></span>       |  

<span data-ttu-id="acb13-129">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="acb13-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="acb13-130">&Dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="acb13-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="acb13-131">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="acb13-131">Browser</span></span>                          | <span data-ttu-id="acb13-132">Sürüm</span><span class="sxs-lookup"><span data-stu-id="acb13-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="acb13-133">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="acb13-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="acb13-134">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-134">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-135">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="acb13-135">Google Chrome, including Android</span></span> | <span data-ttu-id="acb13-136">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-136">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="acb13-137">Microsoft Edge</span></span>                   | <span data-ttu-id="acb13-138">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-138">Current&dagger;</span></span> |
| <span data-ttu-id="acb13-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="acb13-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="acb13-140">üst&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-140">11&Dagger;</span></span>      |
| <span data-ttu-id="acb13-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="acb13-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="acb13-142">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb13-142">Current&dagger;</span></span> |

<span data-ttu-id="acb13-143">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="acb13-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="acb13-144">&Dagger;Ek polydolgular gereklidir.</span><span class="sxs-lookup"><span data-stu-id="acb13-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="acb13-145">Örneğin, bir paket aracılığıyla taahhüt eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) .</span><span class="sxs-lookup"><span data-stu-id="acb13-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="acb13-146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="acb13-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
