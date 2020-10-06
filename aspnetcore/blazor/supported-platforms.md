---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
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
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754547"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="aeaab-103">BlazorDesteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aeaab-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="aeaab-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="aeaab-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aeaab-105">Blazor WebAssembly ve Blazor Server Aşağıdaki tabloda gösterilen tarayıcılarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="aeaab-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="aeaab-106">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="aeaab-106">Browser</span></span>                          | <span data-ttu-id="aeaab-107">Sürüm</span><span class="sxs-lookup"><span data-stu-id="aeaab-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="aeaab-108">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="aeaab-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="aeaab-109">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-109">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-110">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="aeaab-110">Google Chrome, including Android</span></span> | <span data-ttu-id="aeaab-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-111">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="aeaab-112">Microsoft Edge</span></span>                   | <span data-ttu-id="aeaab-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-113">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="aeaab-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="aeaab-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-115">Current&dagger;</span></span> |  

<span data-ttu-id="aeaab-116">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="aeaab-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="aeaab-117">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="aeaab-117">Browser</span></span>                          | <span data-ttu-id="aeaab-118">Sürüm</span><span class="sxs-lookup"><span data-stu-id="aeaab-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="aeaab-119">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="aeaab-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="aeaab-120">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-120">Current&dagger;</span></span>       |
| <span data-ttu-id="aeaab-121">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="aeaab-121">Google Chrome, including Android</span></span> | <span data-ttu-id="aeaab-122">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-122">Current&dagger;</span></span>       |
| <span data-ttu-id="aeaab-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="aeaab-123">Microsoft Edge</span></span>                   | <span data-ttu-id="aeaab-124">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-124">Current&dagger;</span></span>       |
| <span data-ttu-id="aeaab-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="aeaab-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="aeaab-126">Desteklenmiyor&Dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="aeaab-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="aeaab-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="aeaab-128">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-128">Current&dagger;</span></span>       |  

<span data-ttu-id="aeaab-129">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="aeaab-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="aeaab-130">&Dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="aeaab-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="aeaab-131">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="aeaab-131">Browser</span></span>                          | <span data-ttu-id="aeaab-132">Sürüm</span><span class="sxs-lookup"><span data-stu-id="aeaab-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="aeaab-133">İOS dahil Apple Safari</span><span class="sxs-lookup"><span data-stu-id="aeaab-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="aeaab-134">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-134">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-135">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="aeaab-135">Google Chrome, including Android</span></span> | <span data-ttu-id="aeaab-136">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-136">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="aeaab-137">Microsoft Edge</span></span>                   | <span data-ttu-id="aeaab-138">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-138">Current&dagger;</span></span> |
| <span data-ttu-id="aeaab-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="aeaab-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="aeaab-140">üst&Dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-140">11&Dagger;</span></span>      |
| <span data-ttu-id="aeaab-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="aeaab-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="aeaab-142">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeaab-142">Current&dagger;</span></span> |

<span data-ttu-id="aeaab-143">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="aeaab-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="aeaab-144">&Dagger;Ek polydolgular gereklidir.</span><span class="sxs-lookup"><span data-stu-id="aeaab-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="aeaab-145">Örneğin, bir paket aracılığıyla taahhüt eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) .</span><span class="sxs-lookup"><span data-stu-id="aeaab-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aeaab-146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="aeaab-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
