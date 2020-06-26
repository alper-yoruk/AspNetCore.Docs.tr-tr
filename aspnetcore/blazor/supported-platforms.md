---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401941"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="66450-103">BlazorDesteklenen platformları ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66450-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="66450-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="66450-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="66450-105">Tarayıcı gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="66450-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="66450-106">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="66450-106">Browser</span></span>                          | <span data-ttu-id="66450-107">Sürüm</span><span class="sxs-lookup"><span data-stu-id="66450-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="66450-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="66450-108">Microsoft Edge</span></span>                   | <span data-ttu-id="66450-109">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-109">Current</span></span>               |
| <span data-ttu-id="66450-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="66450-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="66450-111">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-111">Current</span></span>               |
| <span data-ttu-id="66450-112">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="66450-112">Google Chrome, including Android</span></span> | <span data-ttu-id="66450-113">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-113">Current</span></span>               |
| <span data-ttu-id="66450-114">İOS dahil Safari</span><span class="sxs-lookup"><span data-stu-id="66450-114">Safari, including iOS</span></span>            | <span data-ttu-id="66450-115">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-115">Current</span></span>               |
| <span data-ttu-id="66450-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="66450-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="66450-117">Desteklenmiyor&dagger;</span><span class="sxs-lookup"><span data-stu-id="66450-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="66450-118">&dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="66450-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="66450-119">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="66450-119">Browser</span></span>                          | <span data-ttu-id="66450-120">Sürüm</span><span class="sxs-lookup"><span data-stu-id="66450-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="66450-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="66450-121">Microsoft Edge</span></span>                   | <span data-ttu-id="66450-122">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-122">Current</span></span>    |
| <span data-ttu-id="66450-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="66450-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="66450-124">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-124">Current</span></span>    |
| <span data-ttu-id="66450-125">Android dahil Google Chrome</span><span class="sxs-lookup"><span data-stu-id="66450-125">Google Chrome, including Android</span></span> | <span data-ttu-id="66450-126">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-126">Current</span></span>    |
| <span data-ttu-id="66450-127">İOS dahil Safari</span><span class="sxs-lookup"><span data-stu-id="66450-127">Safari, including iOS</span></span>            | <span data-ttu-id="66450-128">Geçerli</span><span class="sxs-lookup"><span data-stu-id="66450-128">Current</span></span>    |
| <span data-ttu-id="66450-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="66450-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="66450-130">üst&dagger;</span><span class="sxs-lookup"><span data-stu-id="66450-130">11&dagger;</span></span> |

<span data-ttu-id="66450-131">&dagger;Ek polydolgular gereklidir (örneğin, bu, bir paket ile eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) ).</span><span class="sxs-lookup"><span data-stu-id="66450-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66450-132">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="66450-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
