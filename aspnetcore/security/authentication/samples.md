---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060345"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="7e015-103">ASP.NET Core için kimlik doğrulama örnekleri</span><span class="sxs-lookup"><span data-stu-id="7e015-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="7e015-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7e015-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e015-105">[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="7e015-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="7e015-106">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="7e015-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="7e015-107">[:::no-loc(Cookie)::: yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="7e015-107">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="7e015-108">Özel ilke sağlayıcısı-ıauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="7e015-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="7e015-109">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="7e015-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="7e015-110">[Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="7e015-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="7e015-111">:::no-loc(cookie):::İsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="7e015-111">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="7e015-112">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="7e015-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="7e015-113">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7e015-113">Run the samples</span></span>

* <span data-ttu-id="7e015-114">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="7e015-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="7e015-115">Örneğin, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="7e015-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="7e015-116">[ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.</span><span class="sxs-lookup"><span data-stu-id="7e015-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="7e015-117">ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7e015-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="7e015-118">*Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7e015-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e015-119">[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="7e015-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="7e015-120">Talepleri dönüştürme</span><span class="sxs-lookup"><span data-stu-id="7e015-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="7e015-121">[:::no-loc(Cookie)::: yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="7e015-121">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="7e015-122">Özel ilke sağlayıcısı-ıauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="7e015-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="7e015-123">Dinamik kimlik doğrulama şemaları ve seçenekleri</span><span class="sxs-lookup"><span data-stu-id="7e015-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="7e015-124">[Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="7e015-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="7e015-125">:::no-loc(cookie):::İsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="7e015-125">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="7e015-126">Statik dosyalara erişimi kısıtlar</span><span class="sxs-lookup"><span data-stu-id="7e015-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="7e015-127">Örnekleri çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7e015-127">Run the samples</span></span>

* <span data-ttu-id="7e015-128">Bir [dal](https://github.com/dotnet/AspNetCore)seçin.</span><span class="sxs-lookup"><span data-stu-id="7e015-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="7e015-129">Örneğin, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="7e015-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="7e015-130">[ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.</span><span class="sxs-lookup"><span data-stu-id="7e015-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="7e015-131">ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7e015-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="7e015-132">*Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7e015-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
