---
title: .NET Core 'da gRPC 'ye giriş
author: juntaoluo
description: Kestrel Server ve ASP.NET Core Stack ile gRPC hizmetleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 80f44e3845cc1e3c87d5d657807a318eb65e6c6f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059903"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="d5f18-103">.NET Core 'da gRPC 'ye giriş</span><span class="sxs-lookup"><span data-stu-id="d5f18-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="d5f18-104">[John Luo](https://github.com/juntaoluo) ve [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d5f18-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d5f18-105">[GRPC](https://grpc.io/docs/guides/) , dilden bağımsız, yüksek performanslı bir uzak yordam ÇAĞRıSı (RPC) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="d5f18-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="d5f18-106">gRPC’nin başlıca avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d5f18-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="d5f18-107">Modern, yüksek performanslı, hafif RPC çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="d5f18-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="d5f18-108">Varsayılan olarak Protokol Arabellekleri kullanan, dilden bağımsız uygulamalara olanak veren Sözleşme öncelikli API geliştirme.</span><span class="sxs-lookup"><span data-stu-id="d5f18-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="d5f18-109">Kesin tür belirtilmiş sunucular ve istemciler oluşturmak için çok sayıda dilde kullanılabilen araçlar.</span><span class="sxs-lookup"><span data-stu-id="d5f18-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="d5f18-110">İstemci, sunucu ve iki yönlü akış çağrılarına yönelik destek.</span><span class="sxs-lookup"><span data-stu-id="d5f18-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="d5f18-111">Protobuf ikili serileştirme ile azaltılmış ağ kullanımı.</span><span class="sxs-lookup"><span data-stu-id="d5f18-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="d5f18-112">Bu avantajlar, gRPC 'yi ideal hale getirir:</span><span class="sxs-lookup"><span data-stu-id="d5f18-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="d5f18-113">Verimlilik açısından kritik olan hafif mikro hizmetler.</span><span class="sxs-lookup"><span data-stu-id="d5f18-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="d5f18-114">Geliştirme için birden fazla dilin gerekli olduğu çok yönlü sistemleri.</span><span class="sxs-lookup"><span data-stu-id="d5f18-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="d5f18-115">Akış isteklerini veya yanıtlarını işlemek için gereken noktadan noktaya gerçek zamanlı hizmetler.</span><span class="sxs-lookup"><span data-stu-id="d5f18-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="d5f18-116">. Proto dosyaları için C# araçları desteği</span><span class="sxs-lookup"><span data-stu-id="d5f18-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="d5f18-117">gRPC, API geliştirmesi için bir sözleşmenin ilk yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5f18-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="d5f18-118">Hizmetler ve mesajlar *\* . proto* dosyalarında tanımlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="d5f18-118">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="d5f18-119">Hizmetler, istemciler ve iletiler için .NET türleri bir projedeki *\* . proto* dosyaları eklenerek otomatik olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d5f18-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="d5f18-120">[GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d5f18-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="d5f18-121">*\* . Proto* dosyalarını `<Protobuf>` öğe grubuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d5f18-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="d5f18-122">GRPC araç desteği hakkında daha fazla bilgi için bkz <xref:grpc/basics> ..</span><span class="sxs-lookup"><span data-stu-id="d5f18-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="d5f18-123">ASP.NET Core gRPC Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5f18-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="d5f18-124">gRPC Hizmetleri, ASP.NET Core üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5f18-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="d5f18-125">Hizmetler, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi popüler ASP.NET Core özelliklerle tam tümleştirmeye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d5f18-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="d5f18-126">GRPC hizmeti proje şablonu bir başlatıcı hizmeti sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5f18-126">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="d5f18-127">`GreeterService``GreeterBase`, `Greeter` *\* . proto* dosyasındaki hizmetten oluşturulan türden devralır.</span><span class="sxs-lookup"><span data-stu-id="d5f18-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="d5f18-128">Hizmet, *Startup.cs* içindeki istemciler için erişilebilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="d5f18-128">The service is made accessible to clients in *Startup.cs* :</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="d5f18-129">ASP.NET Core 'de gRPC hizmetleri hakkında daha fazla bilgi edinmek için bkz <xref:grpc/aspnetcore> ..</span><span class="sxs-lookup"><span data-stu-id="d5f18-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="d5f18-130">Bir .NET istemcisiyle gRPC hizmetlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="d5f18-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="d5f18-131">gRPC istemcileri [ *\* . proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir.</span><span class="sxs-lookup"><span data-stu-id="d5f18-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="d5f18-132">Somut gRPC istemcisinde *\* . proto* dosyasındaki GRPC hizmetine çeviren yöntemler vardır.</span><span class="sxs-lookup"><span data-stu-id="d5f18-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="d5f18-133">GRPC istemcisi, bir gRPC hizmeti ile uzun süreli bağlantıyı temsil eden bir kanal kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5f18-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="d5f18-134">Kullanılarak bir kanal oluşturulabilir `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="d5f18-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="d5f18-135">İstemci oluşturma ve farklı hizmet yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:grpc/client> ..</span><span class="sxs-lookup"><span data-stu-id="d5f18-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5f18-136">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d5f18-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
