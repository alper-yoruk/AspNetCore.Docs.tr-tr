---
title: C# içeren gRPC hizmetleri
author: juntaoluo
description: C# ile gRPC hizmetleri yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664208"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="417e5-103">C ile gRPC hizmetleri\#</span><span class="sxs-lookup"><span data-stu-id="417e5-103">gRPC services with C\#</span></span>

<span data-ttu-id="417e5-104">Bu belge, C#'a [gRPC](https://grpc.io/docs/guides/) uygulamaları yazmak için gereken kavramları özetler.</span><span class="sxs-lookup"><span data-stu-id="417e5-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="417e5-105">Burada ele alınan konular hem [C-çekirdek](https://grpc.io/blog/grpc-stacks)tabanlı hem de ASP.NET Core tabanlı gRPC uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="417e5-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="417e5-106">proto dosyası</span><span class="sxs-lookup"><span data-stu-id="417e5-106">proto file</span></span>

<span data-ttu-id="417e5-107">gRPC, API geliştirme için sözleşmeye ilk yaklaşım kullanır.</span><span class="sxs-lookup"><span data-stu-id="417e5-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="417e5-108">Protokol arabellekleri (protobuf) varsayılan olarak Arabirim Tasarım Dili (IDL) olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="417e5-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="417e5-109">.proto dosyası şunları içerir: \* \*\*</span><span class="sxs-lookup"><span data-stu-id="417e5-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="417e5-110">gRPC hizmetinin tanımı.</span><span class="sxs-lookup"><span data-stu-id="417e5-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="417e5-111">İstemciler ve sunucular arasında gönderilen iletiler.</span><span class="sxs-lookup"><span data-stu-id="417e5-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="417e5-112">Protobuf dosyalarının sözdizimi hakkında daha fazla bilgi için [resmi belgelere (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)bakın.</span><span class="sxs-lookup"><span data-stu-id="417e5-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="417e5-113">Örneğin, [gRPC hizmeti ile başlayın'da](xref:tutorials/grpc/grpc-start)kullanılan *greet.proto* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="417e5-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="417e5-114">Bir `Greeter` hizmeti tanımlar.</span><span class="sxs-lookup"><span data-stu-id="417e5-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="417e5-115">Hizmet `Greeter` bir `SayHello` çağrı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="417e5-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="417e5-116">`SayHello`ileti `HelloRequest` gönderir ve `HelloReply` bir ileti alır:</span><span class="sxs-lookup"><span data-stu-id="417e5-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="417e5-117">C\# uygulamasına .proto dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="417e5-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="417e5-118">.proto `<Protobuf>` dosyası, öğe grubuna eklenerek projeye dahil edilir: \* \*\*</span><span class="sxs-lookup"><span data-stu-id="417e5-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="417e5-119">C# .proto dosyaları için araçlama desteği</span><span class="sxs-lookup"><span data-stu-id="417e5-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="417e5-120">Takım paketi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) \* \*.proto\* dosyaları C# varlıkları oluşturmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="417e5-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="417e5-121">Oluşturulan varlıklar (dosyalar):</span><span class="sxs-lookup"><span data-stu-id="417e5-121">The generated assets (files):</span></span>

* <span data-ttu-id="417e5-122">Proje her oluşturulduğunda gerektiği gibi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="417e5-123">Projeye eklenmez veya kaynak denetimine işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="417e5-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="417e5-124">*Obj* dizininde bulunan bir yapı artefakt vardır.</span><span class="sxs-lookup"><span data-stu-id="417e5-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="417e5-125">Bu paket hem sunucu hem de istemci projeleri tarafından gereklidir.</span><span class="sxs-lookup"><span data-stu-id="417e5-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="417e5-126">Metapaket `Grpc.AspNetCore` bir başvuru `Grpc.Tools`içerir .</span><span class="sxs-lookup"><span data-stu-id="417e5-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="417e5-127">Sunucu projeleri `Grpc.AspNetCore` Visual Studio'daki Paket Yöneticisi'ni `<PackageReference>` kullanarak veya proje dosyasına bir ekleyerek ekleyebilir:</span><span class="sxs-lookup"><span data-stu-id="417e5-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="417e5-128">İstemci projeleri, gRPC istemcisini kullanmak için gereken diğer paketlerin yanında doğrudan başvurulmalıdır. `Grpc.Tools`</span><span class="sxs-lookup"><span data-stu-id="417e5-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="417e5-129">Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`aşağıdakilerle işaretlenir:</span><span class="sxs-lookup"><span data-stu-id="417e5-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="417e5-130">Oluşturulan C# varlıkları</span><span class="sxs-lookup"><span data-stu-id="417e5-130">Generated C# assets</span></span>

<span data-ttu-id="417e5-131">Araç paketi, dahil \* \*edilen .proto\* dosyalarında tanımlanan iletileri temsil eden C# türlerini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="417e5-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="417e5-132">Sunucu tarafındaki varlıklar için soyut bir hizmet taban türü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="417e5-133">Temel *tür,.proto* dosyasında bulunan tüm gRPC çağrılarının tanımlarını içerir.</span><span class="sxs-lookup"><span data-stu-id="417e5-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="417e5-134">Bu taban türünden türeyen ve gRPC çağrıları için mantığı uygulayan somut bir hizmet uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="417e5-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="417e5-135">`greet.proto`Daha önce açıklanan örnek için, `GreeterBase` sanal `SayHello` bir yöntem içeren soyut bir tür oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="417e5-136">Somut bir `GreeterService` uygulama yöntemi geçersiz kılar ve gRPC çağrısını işleme mantığını uygular.</span><span class="sxs-lookup"><span data-stu-id="417e5-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="417e5-137">İstemci tarafındaki varlıklar için somut bir istemci türü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="417e5-138">*.proto* dosyasındaki gRPC çağrıları, çağrılabilecek somut türdeki yöntemlere dönüştürülr.</span><span class="sxs-lookup"><span data-stu-id="417e5-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="417e5-139">`greet.proto`Daha önce açıklanan örnek için somut `GreeterClient` bir tür oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="417e5-140">Sunucuya gRPC çağrısı başlatmak için arayın. `GreeterClient.SayHelloAsync`</span><span class="sxs-lookup"><span data-stu-id="417e5-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="417e5-141">Varsayılan olarak, `<Protobuf>` madde grubuna dahil edilen her \* \*.proto\* dosyası için sunucu ve istemci varlıkları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="417e5-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="417e5-142">Bir sunucu projesinde yalnızca sunucu varlıklarının `GrpcServices` oluşturulduğundan emin `Server`olmak için öznitelik .</span><span class="sxs-lookup"><span data-stu-id="417e5-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="417e5-143">Benzer şekilde, öznitelik istemci `Client` projelerinde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="417e5-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="417e5-144">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="417e5-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
