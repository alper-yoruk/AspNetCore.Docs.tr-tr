---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630750"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="ca1e6-103">ASP.NET Core için kimlik doğrulama örnekleri</span><span class="sxs-lookup"><span data-stu-id="ca1e6-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="ca1e6-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca1e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca1e6-105">[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="ca1e6-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ca1e6-106">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="ca1e6-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="ca1e6-107">[Cookie yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="ca1e6-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="ca1e6-108">Özel ilke sağlayıcısı-ıauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="ca1e6-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ca1e6-109">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="ca1e6-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="ca1e6-110">[Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="ca1e6-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="ca1e6-111">cookieİsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="ca1e6-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ca1e6-112">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="ca1e6-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ca1e6-113">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="ca1e6-113">Run the samples</span></span>

* <span data-ttu-id="ca1e6-114">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ca1e6-115">Örneğin, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="ca1e6-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="ca1e6-116">[ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ca1e6-117">ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ca1e6-118">*Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ca1e6-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca1e6-119">[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="ca1e6-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ca1e6-120">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="ca1e6-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="ca1e6-121">[Cookie yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="ca1e6-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="ca1e6-122">Özel ilke sağlayıcısı-ıauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="ca1e6-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ca1e6-123">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="ca1e6-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="ca1e6-124">[Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="ca1e6-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="ca1e6-125">cookieİsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="ca1e6-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ca1e6-126">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="ca1e6-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ca1e6-127">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="ca1e6-127">Run the samples</span></span>

* <span data-ttu-id="ca1e6-128">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ca1e6-129">Örneğin, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="ca1e6-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="ca1e6-130">[ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ca1e6-131">ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ca1e6-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ca1e6-132">*Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ca1e6-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
