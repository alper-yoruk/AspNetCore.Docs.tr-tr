---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308221"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="41b3f-103">ASP.NET Core için kimlik doğrulama örnekleri</span><span class="sxs-lookup"><span data-stu-id="41b3f-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="41b3f-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41b3f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41b3f-105">[ASP.NET Core deposu,](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="41b3f-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="41b3f-106">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="41b3f-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="41b3f-107">Çerez kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="41b3f-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="41b3f-108">Özel ilke sağlayıcısı - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="41b3f-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="41b3f-109">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="41b3f-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="41b3f-110">Dış talepler</span><span class="sxs-lookup"><span data-stu-id="41b3f-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="41b3f-111">İsteğe dayalı çerez ve başka bir kimlik doğrulama düzeni arasında seçme</span><span class="sxs-lookup"><span data-stu-id="41b3f-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="41b3f-112">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="41b3f-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="41b3f-113">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="41b3f-113">Run the samples</span></span>

* <span data-ttu-id="41b3f-114">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="41b3f-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="41b3f-115">Örneğin, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="41b3f-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="41b3f-116">ASP.NET [Core deposunu](https://github.com/dotnet/AspNetCore)klonla veya indirin.</span><span class="sxs-lookup"><span data-stu-id="41b3f-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="41b3f-117">ASP.NET Core deposunun klonuyla eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="41b3f-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="41b3f-118">*AspNetCore/src/Security/samples'deki* bir örneğe gidin `dotnet run`ve örneği .</span><span class="sxs-lookup"><span data-stu-id="41b3f-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41b3f-119">[ASP.NET Core deposu,](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="41b3f-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="41b3f-120">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="41b3f-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="41b3f-121">Çerez kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="41b3f-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="41b3f-122">Özel ilke sağlayıcısı - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="41b3f-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="41b3f-123">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="41b3f-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="41b3f-124">Dış talepler</span><span class="sxs-lookup"><span data-stu-id="41b3f-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="41b3f-125">İsteğe dayalı çerez ve başka bir kimlik doğrulama düzeni arasında seçme</span><span class="sxs-lookup"><span data-stu-id="41b3f-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="41b3f-126">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="41b3f-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="41b3f-127">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="41b3f-127">Run the samples</span></span>

* <span data-ttu-id="41b3f-128">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="41b3f-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="41b3f-129">Örneğin, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="41b3f-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="41b3f-130">ASP.NET [Core deposunu](https://github.com/dotnet/AspNetCore)klonla veya indirin.</span><span class="sxs-lookup"><span data-stu-id="41b3f-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="41b3f-131">ASP.NET Core deposunun klonuyla eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="41b3f-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="41b3f-132">*AspNetCore/src/Security/samples'deki* bir örneğe gidin `dotnet run`ve örneği .</span><span class="sxs-lookup"><span data-stu-id="41b3f-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
