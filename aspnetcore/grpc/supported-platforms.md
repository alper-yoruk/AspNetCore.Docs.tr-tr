---
title: .NET destekli platformlar üzerinde gRPC
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
ms.openlocfilehash: 88d371f460839261b618a32564a723c257b0b119
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217498"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="a1ccb-103">.NET destekli platformlar üzerinde gRPC</span><span class="sxs-lookup"><span data-stu-id="a1ccb-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="a1ccb-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a1ccb-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a1ccb-105">Bu makalede, .NET ile gRPC kullanmaya yönelik gereksinimler ve desteklenen platformlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="a1ccb-106">gRPC, HTTP/2 ' de bulunan gelişmiş özelliklerden yararlanır.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="a1ccb-107">HTTP/2 her yerde desteklenmez, ancak gRPC için HTTP/1.1 kullanan ikinci bir hat biçimi mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="a1ccb-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="a1ccb-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -HTTP/2 üzerinden gRPC, gRPC 'nin genellikle kullanıldığı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="a1ccb-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web, gRPC protokolünü HTTP/1.1 ile uyumlu olacak şekilde değiştirir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="a1ccb-110">gRPC-Web daha fazla yerde kullanılabilir, özellikle de tarayıcı uygulamaları tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="a1ccb-111">İki gelişmiş gRPC özelliği artık desteklenmiyor: istemci akışı ve çift yönlü akış.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="a1ccb-112">.NET üzerinde gRPC, her iki kablo biçimini destekler.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="a1ccb-113">HTTP/2 üzerinden gRPC varsayılan olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="a1ccb-114">GRPC-Web ' i ayarlama hakkında daha fazla bilgi için bkz <xref:grpc/browser> ..</span><span class="sxs-lookup"><span data-stu-id="a1ccb-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="a1ccb-115">Cihaz gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="a1ccb-115">Device requirements</span></span>

<span data-ttu-id="a1ccb-116">.NET üzerinde gRPC, .NET Core 'un desteklediği tüm cihazları destekler.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a1ccb-117">Windows</span><span class="sxs-lookup"><span data-stu-id="a1ccb-117">Windows</span></span>
> * <span data-ttu-id="a1ccb-118">Linux</span><span class="sxs-lookup"><span data-stu-id="a1ccb-118">Linux</span></span>
> * <span data-ttu-id="a1ccb-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1ccb-119">macOS&dagger;</span></span>
> * <span data-ttu-id="a1ccb-120">Browsers (Tarayıcılar)&Dagger;</span><span class="sxs-lookup"><span data-stu-id="a1ccb-120">Browsers&Dagger;</span></span>

<span data-ttu-id="a1ccb-121">&dagger;[MacOS, https ile ASP.NET Core uygulamalarının barındırılmasını desteklemez](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="a1ccb-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="a1ccb-122">macOS üzerinde gRPC istemcileri, HTTPS kullanan uzak hizmetleri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="a1ccb-123">&Dagger;Blazor WebAssembly uygulamalar, GRPC hizmetlerini gRPC-Web ile çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="a1ccb-124">ASP.NET Core sunucusu gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="a1ccb-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="a1ccb-125">gRPC Hizmetleri, tüm yerleşik ASP.NET Core sunucularında barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a1ccb-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1ccb-126">Kestrel</span></span>
> * <span data-ttu-id="a1ccb-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="a1ccb-127">TestServer</span></span>
> * <span data-ttu-id="a1ccb-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1ccb-128">IIS&dagger;</span></span>
> * <span data-ttu-id="a1ccb-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1ccb-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="a1ccb-130">&dagger;IIS ve HTTP.sys .NET 5 ve Windows 10 derleme 20241 veya üstünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="a1ccb-131">Daha fazla bilgi için bkz. <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="a1ccb-132">.NET sürüm gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="a1ccb-132">.NET version requirements</span></span>

<span data-ttu-id="a1ccb-133">.NET üzerinde gRPC .NET Core 3 ve .NET 5 veya üstünü destekler.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-133">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a1ccb-134">.NET 5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a1ccb-134">.NET 5 or later</span></span>
> * <span data-ttu-id="a1ccb-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="a1ccb-135">.NET Core 3</span></span>

<span data-ttu-id="a1ccb-136">.NET üzerinde gRPC .NET Framework ve Xamarin üzerinde çalışmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-136">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="a1ccb-137">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) , .NET Framework ve Xamarin 'i destekleyen üçüncü taraf bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="a1ccb-138">gRPC C-Core, Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="a1ccb-139">Azure hizmetleri</span><span class="sxs-lookup"><span data-stu-id="a1ccb-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="a1ccb-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="a1ccb-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="a1ccb-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1ccb-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="a1ccb-142">&dagger;Azure App Service, gRPC 'yi HTTP/2 üzerinden barındırmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="a1ccb-143">gRPC-Web, uyumlu bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="a1ccb-144">Azure App Service 'de HTTP/2 ile gRPC desteğinin geliştirilmesi için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="a1ccb-145">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/9020)bakın.</span><span class="sxs-lookup"><span data-stu-id="a1ccb-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1ccb-146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a1ccb-146">Additional resources</span></span>

* [<span data-ttu-id="a1ccb-147">gRPC C# Core-Library</span><span class="sxs-lookup"><span data-stu-id="a1ccb-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
