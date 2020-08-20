---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625953"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="148c5-103">BlazorDesteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="148c5-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="148c5-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="148c5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="148c5-105">Tarayıcı gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="148c5-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="148c5-106">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="148c5-106">Browser</span></span>                          | <span data-ttu-id="148c5-107">Sürüm</span><span class="sxs-lookup"><span data-stu-id="148c5-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="148c5-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="148c5-108">Microsoft Edge</span></span>                   | <span data-ttu-id="148c5-109">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-109">Current</span></span>               |
| <span data-ttu-id="148c5-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="148c5-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="148c5-111">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-111">Current</span></span>               |
| <span data-ttu-id="148c5-112">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="148c5-112">Google Chrome, including Android</span></span> | <span data-ttu-id="148c5-113">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-113">Current</span></span>               |
| <span data-ttu-id="148c5-114">İOS dahil Safari</span><span class="sxs-lookup"><span data-stu-id="148c5-114">Safari, including iOS</span></span>            | <span data-ttu-id="148c5-115">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-115">Current</span></span>               |
| <span data-ttu-id="148c5-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="148c5-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="148c5-117">Desteklenmiyor&dagger;</span><span class="sxs-lookup"><span data-stu-id="148c5-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="148c5-118">&dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="148c5-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="148c5-119">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="148c5-119">Browser</span></span>                          | <span data-ttu-id="148c5-120">Sürüm</span><span class="sxs-lookup"><span data-stu-id="148c5-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="148c5-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="148c5-121">Microsoft Edge</span></span>                   | <span data-ttu-id="148c5-122">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-122">Current</span></span>    |
| <span data-ttu-id="148c5-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="148c5-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="148c5-124">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-124">Current</span></span>    |
| <span data-ttu-id="148c5-125">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="148c5-125">Google Chrome, including Android</span></span> | <span data-ttu-id="148c5-126">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-126">Current</span></span>    |
| <span data-ttu-id="148c5-127">İOS dahil Safari</span><span class="sxs-lookup"><span data-stu-id="148c5-127">Safari, including iOS</span></span>            | <span data-ttu-id="148c5-128">Geçerli</span><span class="sxs-lookup"><span data-stu-id="148c5-128">Current</span></span>    |
| <span data-ttu-id="148c5-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="148c5-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="148c5-130">üst&dagger;</span><span class="sxs-lookup"><span data-stu-id="148c5-130">11&dagger;</span></span> |

<span data-ttu-id="148c5-131">&dagger;Ek polydolgular gereklidir (örneğin, bu, bir paket ile eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) ).</span><span class="sxs-lookup"><span data-stu-id="148c5-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="148c5-132">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="148c5-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
