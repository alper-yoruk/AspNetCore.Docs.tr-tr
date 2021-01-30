---
title: .NET tarafından desteklenen platformlar için gRPC
author: jamesnk
description: .NET üzerinde gRPC için desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057717"
---
# <a name="grpc-for-net-supported-platforms"></a><span data-ttu-id="b9fe2-103">.NET tarafından desteklenen platformlar için gRPC</span><span class="sxs-lookup"><span data-stu-id="b9fe2-103">gRPC for .NET supported platforms</span></span>

<span data-ttu-id="b9fe2-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b9fe2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b9fe2-105">Bu makalede, .NET ile gRPC kullanmaya yönelik gereksinimler ve desteklenen platformlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="b9fe2-106">gRPC, daha gelişmiş özelliklerden bazıları için HTTP/2 kullanmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-106">gRPC is designed to use HTTP/2 for some of its more advanced features.</span></span> <span data-ttu-id="b9fe2-107">GRPC kullanımını engelleyebilen her yerde HTTP/2 desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-107">HTTP/2 isn't supported everywhere which can prevent using gRPC.</span></span> <span data-ttu-id="b9fe2-108">Bu nedenle, istemciler ve sunucular arasında gRPC çağrıları göndermek için HTTP/1.1 ile uyumlu ikinci bir hat biçimi vardır:</span><span class="sxs-lookup"><span data-stu-id="b9fe2-108">Because of this there is second wire-format that is compatible with HTTP/1.1 for sending gRPC calls between clients and servers:</span></span>

* <span data-ttu-id="b9fe2-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -HTTP/2 üzerinden gRPC, gRPC 'nin genellikle kullanıldığı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="b9fe2-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web, gRPC protokolünü HTTP/1.1 ile uyumlu olacak şekilde değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="b9fe2-111">gRPC-Web daha fazla yerde kullanılabilir, özellikle de tarayıcı uygulamaları tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-111">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="b9fe2-112">İki gelişmiş gRPC özelliği artık desteklenmiyor: istemci akışı ve çift yönlü akış.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-112">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="b9fe2-113">.NET için gRPC, her iki kablo biçimini destekler.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-113">gRPC for .NET supports both wire-formats.</span></span> <span data-ttu-id="b9fe2-114">GRPC-Web ' i ayarlama hakkında daha fazla bilgi için bkz <xref:grpc/browser> ..</span><span class="sxs-lookup"><span data-stu-id="b9fe2-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="b9fe2-115">Cihaz gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="b9fe2-115">Device requirements</span></span>

<span data-ttu-id="b9fe2-116">.NET için gRPC, .NET Core 'un desteklediği tüm cihazları destekler.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-116">gRPC for .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b9fe2-117">Windows</span><span class="sxs-lookup"><span data-stu-id="b9fe2-117">Windows</span></span>
> * <span data-ttu-id="b9fe2-118">Linux</span><span class="sxs-lookup"><span data-stu-id="b9fe2-118">Linux</span></span>
> * <span data-ttu-id="b9fe2-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9fe2-119">macOS&dagger;</span></span>
> * <span data-ttu-id="b9fe2-120">Browsers (Tarayıcılar)&Dagger;</span><span class="sxs-lookup"><span data-stu-id="b9fe2-120">Browsers&Dagger;</span></span>

<span data-ttu-id="b9fe2-121">&dagger;MacOS 'ta barındırılan ASP.NET Core uygulamalar HTTPS 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-121">&dagger;ASP.NET Core apps hosted on macOS don't support HTTPS.</span></span> <span data-ttu-id="b9fe2-122">macOS üzerinde gRPC istemcileri, uzak hizmetleri çağırırken HTTPS kullanmaya devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-122">gRPC clients on macOS can still use HTTPS when calling remote services.</span></span>

<span data-ttu-id="b9fe2-123">&Dagger;Blazor WebAssembly uygulamalar, GRPC hizmetlerini gRPC-Web ile çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="b9fe2-124">ASP.NET Core sunucusu gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="b9fe2-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="b9fe2-125">gRPC Hizmetleri, tüm yerleşik ASP.NET Core sunucularında barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b9fe2-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b9fe2-126">Kestrel</span></span>
> * <span data-ttu-id="b9fe2-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="b9fe2-127">TestServer</span></span>
> * <span data-ttu-id="b9fe2-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9fe2-128">IIS&dagger;</span></span>
> * <span data-ttu-id="b9fe2-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9fe2-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="b9fe2-130">&dagger;IIS ve HTTP.sys .NET 5 ve Windows 10 derleme 20241 veya üstünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="b9fe2-131">Daha fazla bilgi için bkz. <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="b9fe2-132">.NET sürüm gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="b9fe2-132">.NET version requirements</span></span>

<span data-ttu-id="b9fe2-133">.NET için gRPC, .NET Core 3 ve .NET 5 veya üstünü destekler.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-133">gRPC for .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b9fe2-134">.NET 5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b9fe2-134">.NET 5 or later</span></span>
> * <span data-ttu-id="b9fe2-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="b9fe2-135">.NET Core 3</span></span>

<span data-ttu-id="b9fe2-136">.NET için gRPC .NET Framework ve Xamarin üzerinde çalışmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-136">gRPC for .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="b9fe2-137">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) , .NET Framework ve Xamarin 'i destekleyen üçüncü taraf bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="b9fe2-138">gRPC C-Core, Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="b9fe2-139">Azure hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b9fe2-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="b9fe2-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="b9fe2-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="b9fe2-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9fe2-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="b9fe2-142">&dagger;Azure App Service, gRPC 'yi HTTP/2 üzerinden barındırmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="b9fe2-143">gRPC-Web, uyumlu bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="b9fe2-144">Azure App Service 'de HTTP/2 ile gRPC desteğinin geliştirilmesi için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="b9fe2-145">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/9020)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9fe2-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9fe2-146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b9fe2-146">Additional resources</span></span>

* [<span data-ttu-id="b9fe2-147">gRPC C# Core-Library</span><span class="sxs-lookup"><span data-stu-id="b9fe2-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
