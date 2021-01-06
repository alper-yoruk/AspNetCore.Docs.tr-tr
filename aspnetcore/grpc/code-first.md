---
title: .NET ile kod ilk gRPC Hizmetleri ve istemcileri
author: jamesnk
description: .NET ile Code-First gRPC yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880637"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="dbe4d-103">.NET ile kod ilk gRPC Hizmetleri ve istemcileri</span><span class="sxs-lookup"><span data-stu-id="dbe4d-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="dbe4d-104">, [James bAyKiNg](https://twitter.com/jamesnk) ve [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="dbe4d-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="dbe4d-105">Code-First gRPC, hizmet ve ileti sözleşmelerini tanımlamak için .NET türlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="dbe4d-106">Kod-ilk bir sistem .NET kullandığında iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="dbe4d-107">.Net hizmeti ve veri sözleşmesi türleri .NET sunucusu ve istemcileri arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="dbe4d-108">`.proto`Dosyalarda ve kod oluşturmada sözleşmeleri tanımlama ihtiyacını önler.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="dbe4d-109">Kod-önce birden çok dile sahip çok yönlü sistemlerinde önerilmez.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="dbe4d-110">.NET hizmeti ve veri sözleşmesi türleri non-.NET platformlarıyla birlikte kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="dbe4d-111">Önce kod kullanılarak yazılmış bir gRPC hizmetini çağırmak için, diğer platformların `.proto` hizmetle eşleşen bir sözleşme oluşturması gerekir.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="dbe4d-112">prototip-net. GRPC</span><span class="sxs-lookup"><span data-stu-id="dbe4d-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dbe4d-113">Prototipli-net için yardım. GRPC, [prototipli-net ' i ziyaret edin. GRPC Web sitesi](https://protobuf-net.github.io/protobuf-net.Grpc/) veya prototip-net üzerinde bir sorun oluşturun [. GRPC GitHub deposu](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="dbe4d-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="dbe4d-114">[prototip-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) bir topluluk projem ve Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="dbe4d-115">Ve ' a ilk kod desteği ekler `Grpc.AspNetCore` `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="dbe4d-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="dbe4d-116">Uygulamanın gRPC hizmetlerini ve iletilerini tanımlamak için özniteliklerle açıklanmış .NET türlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="dbe4d-117">Kodu ilk gRPC hizmeti oluşturmaya yönelik ilk adım, kod sözleşmesini tanımlıyor:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="dbe4d-118">Sunucu ve istemci tarafından paylaşılacak yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="dbe4d-119">Prototip oluşturma [-net. GRPC](https://www.nuget.org/packages/protobuf-net.Grpc) paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="dbe4d-120">Hizmet ve veri sözleşmesi türleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="dbe4d-121">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-121">The preceding code:</span></span>

* <span data-ttu-id="dbe4d-122">`HelloRequest`Ve `HelloReply` iletilerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="dbe4d-123">`IGreeterService`Birli GRPC yöntemiyle sözleşme arabirimini tanımlar `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="dbe4d-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="dbe4d-124">Hizmet sözleşmesi sunucuda uygulanır ve istemciden çağırılır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="dbe4d-125">Hizmet arabirimlerinde tanımlanan yöntemlerin tekil, sunucu akışı, istemci akışı veya çift yönlü akış olmasına bağlı olarak belirli imzalara eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="dbe4d-126">Hizmet sözleşmelerini tanımlama hakkında daha fazla bilgi için bkz [. prototipli-net. GRPC Başlarken belgeleri](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="dbe4d-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="dbe4d-127">Kod oluştur-ilk gRPC hizmeti</span><span class="sxs-lookup"><span data-stu-id="dbe4d-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="dbe4d-128">GRPC kodu-ilk hizmetini bir ASP.NET Core uygulamasına eklemek için:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="dbe4d-129">Prototip oluşturma [-net. GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="dbe4d-130">Paylaşılan kod-sözleşme projesine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="dbe4d-131">Yeni bir `GreeterService.cs` dosya oluşturun ve `IGreeterService` hizmet arabirimini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="dbe4d-132">Dosyayı güncelleştirin `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="dbe4d-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="dbe4d-133">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-133">In the preceding code:</span></span>

* <span data-ttu-id="dbe4d-134">`AddCodeFirstGrpc` önce kodu etkinleştiren Hizmetleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="dbe4d-135">`MapGrpcService<GreeterService>` kod ilk hizmet uç noktasını ekler.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="dbe4d-136">yalnızca kodla uygulanan gRPC Hizmetleri ve `.proto` dosyalar aynı uygulamada birlikte bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="dbe4d-137">Tüm gRPC Hizmetleri [GRPC hizmeti yapılandırmasını](xref:grpc/configuration#configure-services-options)kullanır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="dbe4d-138">Kod oluştur-ilk gRPC istemcisi</span><span class="sxs-lookup"><span data-stu-id="dbe4d-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="dbe4d-139">Bir kod-ilk gRPC istemcisi, gRPC hizmetlerini çağırmak için hizmet sözleşmesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="dbe4d-140">Bir gRPC hizmetini kod ilk istemcisini kullanarak çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="dbe4d-141">Prototip oluşturma [-net. GRPC](https://www.nuget.org/packages/protobuf-net.Grpc) paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="dbe4d-142">Paylaşılan kod-sözleşme projesine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="dbe4d-143">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="dbe4d-143">The preceding code:</span></span>

* <span data-ttu-id="dbe4d-144">Bir gRPC kanalı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="dbe4d-145">Uzantı yöntemiyle kanaldan bir kod ilk istemci oluşturur `CreateGrpcService<IGreeterService>` .</span><span class="sxs-lookup"><span data-stu-id="dbe4d-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="dbe4d-146">İle gRPC hizmetini çağırır `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="dbe4d-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="dbe4d-147">Bir kanaldan kod ilk gRPC istemcisi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="dbe4d-148">Normal bir istemcide olduğu gibi, bir kod ilk istemcisi de kendi [Kanal yapılandırmasını](xref:grpc/configuration#configure-client-options)kullanır.</span><span class="sxs-lookup"><span data-stu-id="dbe4d-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbe4d-149">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dbe4d-149">Additional resources</span></span>

* [<span data-ttu-id="dbe4d-150">prototip-net. GRPC Web sitesi</span><span class="sxs-lookup"><span data-stu-id="dbe4d-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="dbe4d-151">prototip-net. GRPC GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="dbe4d-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
