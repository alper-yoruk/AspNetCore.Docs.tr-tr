---
title: C# içeren gRPC hizmetleri
author: juntaoluo
description: C# ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: 08c755cf078fe71d09e8c7af2cc16f5c427495f1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407297"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="318aa-103">C ile gRPC Hizmetleri\#</span><span class="sxs-lookup"><span data-stu-id="318aa-103">gRPC services with C\#</span></span>

<span data-ttu-id="318aa-104">Bu belgede, C# dilinde [GRPC](https://grpc.io/docs/guides/) uygulamaları yazmak için gereken kavramlar özetlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="318aa-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="318aa-105">Burada ele alınan konular hem [C Core](https://grpc.io/blog/grpc-stacks)tabanlı hem de ASP.NET Core tabanlı GRPC uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="318aa-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="318aa-106">Proto dosyası</span><span class="sxs-lookup"><span data-stu-id="318aa-106">proto file</span></span>

<span data-ttu-id="318aa-107">gRPC, API geliştirmesi için bir sözleşmenin ilk yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="318aa-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="318aa-108">Protokol arabellekleri (protobellek) varsayılan olarak arabirim tasarım dili (IDL) olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="318aa-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="318aa-109">\* \* . Proto\* dosyası şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="318aa-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="318aa-110">GRPC hizmetinin tanımı.</span><span class="sxs-lookup"><span data-stu-id="318aa-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="318aa-111">İstemciler ve sunucular arasında gönderilen iletiler.</span><span class="sxs-lookup"><span data-stu-id="318aa-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="318aa-112">Prototipsiz dosyaların sözdizimi hakkında daha fazla bilgi için, [resmi belgelere (protoarabellek)](https://developers.google.com/protocol-buffers/docs/proto3)bakın.</span><span class="sxs-lookup"><span data-stu-id="318aa-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="318aa-113">Örneğin, [gRPC hizmetini kullanmaya başlama](xref:tutorials/grpc/grpc-start)bölümünde kullanılan *Greet. proto* dosyasını düşünün:</span><span class="sxs-lookup"><span data-stu-id="318aa-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="318aa-114">Bir `Greeter` hizmeti tanımlar.</span><span class="sxs-lookup"><span data-stu-id="318aa-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="318aa-115">`Greeter`Hizmet bir çağrıyı tanımlar `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="318aa-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="318aa-116">`SayHello`bir `HelloRequest` ileti gönderir ve bir `HelloReply` ileti alır:</span><span class="sxs-lookup"><span data-stu-id="318aa-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="318aa-117">C uygulamasına bir. proto dosyası ekleyin \#</span><span class="sxs-lookup"><span data-stu-id="318aa-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="318aa-118">\* \* . Proto\* dosyası bir projeye öğe grubuna eklenerek dahil edilir `<Protobuf>` :</span><span class="sxs-lookup"><span data-stu-id="318aa-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="318aa-119">. Proto dosyaları için C# araçları desteği</span><span class="sxs-lookup"><span data-stu-id="318aa-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="318aa-120">Araçları Paketi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) , \* \* . proto\* dosyalarından C# varlıkları oluşturmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="318aa-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="318aa-121">Oluşturulan varlıklar (dosyalar):</span><span class="sxs-lookup"><span data-stu-id="318aa-121">The generated assets (files):</span></span>

* <span data-ttu-id="318aa-122">, Projenin oluşturulduğu her seferinde gerekli olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="318aa-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="318aa-123">Projeye eklenmez veya kaynak denetimine iade edilmedi.</span><span class="sxs-lookup"><span data-stu-id="318aa-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="318aa-124">*Obj* dizininde bulunan bir yapı yapıtı.</span><span class="sxs-lookup"><span data-stu-id="318aa-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="318aa-125">Bu paket hem sunucu hem de istemci projeleri için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="318aa-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="318aa-126">`Grpc.AspNetCore`Metapackage öğesine bir başvuru içerir `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="318aa-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="318aa-127">Sunucu projeleri `Grpc.AspNetCore` , Visual Studio 'Da Paket Yöneticisi 'ni kullanarak veya bir proje dosyasına bir ekleyerek ekleyebilir `<PackageReference>` :</span><span class="sxs-lookup"><span data-stu-id="318aa-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="318aa-128">İstemci projeleri `Grpc.Tools` , gRPC istemcisini kullanmak için gereken diğer paketlerle birlikte doğrudan başvurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="318aa-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="318aa-129">Çalışma zamanında araç paketi gerekli değildir, bu nedenle bağımlılık şu şekilde işaretlenir `PrivateAssets="All"` :</span><span class="sxs-lookup"><span data-stu-id="318aa-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="318aa-130">Oluşturulan C# varlıkları</span><span class="sxs-lookup"><span data-stu-id="318aa-130">Generated C# assets</span></span>

<span data-ttu-id="318aa-131">Araç paketi, eklenen \* \* . proto\* dosyalarında tanımlanan Iletileri temsil eden C# türlerini üretir.</span><span class="sxs-lookup"><span data-stu-id="318aa-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="318aa-132">Sunucu tarafı varlıklar için, soyut bir hizmet temel türü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="318aa-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="318aa-133">Temel tür, *. proto* dosyasında bulunan tüm GRPC çağrılarının tanımlarını içerir.</span><span class="sxs-lookup"><span data-stu-id="318aa-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="318aa-134">Bu temel türden türetilen somut bir hizmet uygulamasını oluşturun ve gRPC çağrılarının mantığını uygular.</span><span class="sxs-lookup"><span data-stu-id="318aa-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="318aa-135">`greet.proto`Daha önce açıklanan örnek için, `GreeterBase` sanal bir yöntemi içeren bir soyut tür `SayHello` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="318aa-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="318aa-136">Somut bir uygulama `GreeterService` , yöntemini geçersiz kılar ve gRPC çağrısını işleme mantığını uygular.</span><span class="sxs-lookup"><span data-stu-id="318aa-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="318aa-137">İstemci tarafı varlıklar için somut bir istemci türü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="318aa-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="318aa-138">*. Proto* dosyasındaki GRPC çağrıları, çağrılabilecek somut türdeki yöntemlere çevrilir.</span><span class="sxs-lookup"><span data-stu-id="318aa-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="318aa-139">`greet.proto`Daha önce açıklanan örnek için somut bir `GreeterClient` tür oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="318aa-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="318aa-140">`GreeterClient.SayHelloAsync`Sunucuya bir gRPC çağrısı başlatmak için çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="318aa-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="318aa-141">Varsayılan olarak, sunucu ve istemci varlıkları öğe grubuna dahil edilen her \* \* . proto\* dosyası için oluşturulur `<Protobuf>` .</span><span class="sxs-lookup"><span data-stu-id="318aa-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="318aa-142">Sunucu projesinde yalnızca sunucu varlıklarının oluşturulmasını sağlamak için `GrpcServices` özniteliği olarak ayarlanır `Server` .</span><span class="sxs-lookup"><span data-stu-id="318aa-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="318aa-143">Benzer şekilde, özniteliği `Client` istemci projelerinde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="318aa-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="318aa-144">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="318aa-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
