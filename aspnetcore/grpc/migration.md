---
title: GRPC hizmetlerini C-core'dan ASP.NET Core'a geçirme
author: juntaoluo
description: Mevcut bir C çekirdek tabanlı gRPC uygulamasını core yığınının üzerinde çalıştırmak için nasıl ASP.NET öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664138"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="b7a58-103">GRPC hizmetlerini C-core'dan ASP.NET Core'a geçirme</span><span class="sxs-lookup"><span data-stu-id="b7a58-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="b7a58-104">Yazar: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b7a58-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b7a58-105">Temel yığının uygulanması nedeniyle, tüm özellikler [C-core tabanlı gRPC](https://grpc.io/blog/grpc-stacks) uygulamaları ile ASP.NET Çekirdek tabanlı uygulamalar arasında aynı şekilde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="b7a58-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="b7a58-106">Bu belge, iki yığın arasında geçiş için önemli farkları vurgular.</span><span class="sxs-lookup"><span data-stu-id="b7a58-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="b7a58-107">gRPC hizmet uygulama ömrü</span><span class="sxs-lookup"><span data-stu-id="b7a58-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="b7a58-108">ASP.NET Çekirdek yığınında, gRPC hizmetleri varsayılan olarak [kapsamlı](xref:fundamentals/dependency-injection#service-lifetimes)bir ömür boyu ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b7a58-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b7a58-109">Buna karşılık, gRPC C-core varsayılan olarak [tekton ömrü](xref:fundamentals/dependency-injection#service-lifetimes)olan bir hizmete bağlanır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="b7a58-110">Kapsamlı bir ömür, hizmet uygulamasının kapsamlı yaşam sürelerine sahip diğer hizmetleri çözmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="b7a58-111">Örneğin, kapsamlı bir ömür, `DbContext` DI kabından yapıcı enjeksiyon yoluyla da çözülebilir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="b7a58-112">Kapsamlı kullanım ömrünü kullanma:</span><span class="sxs-lookup"><span data-stu-id="b7a58-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="b7a58-113">Her istek için hizmet uygulamasının yeni bir örneği oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b7a58-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="b7a58-114">Uygulama türünde örnek üyeler aracılığıyla istekler arasında durum paylaşımı mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="b7a58-115">Beklenti, paylaşılan durumları DI kapsayıcısında ki tek tonluk bir hizmette depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="b7a58-116">Depolanan paylaşılan durumlar gRPC hizmet uygulamasının oluşturucusunda çözülür.</span><span class="sxs-lookup"><span data-stu-id="b7a58-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="b7a58-117">Hizmet yaşam süreleri hakkında <xref:fundamentals/dependency-injection#service-lifetimes>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="b7a58-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="b7a58-118">Singleton hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="b7a58-118">Add a singleton service</span></span>

<span data-ttu-id="b7a58-119">gRPC C-core uygulamasından ASP.NET Core'a geçişi kolaylaştırmak için, hizmet uygulamasının hizmet ömrünü tek ton kapsamından değiştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="b7a58-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="b7a58-120">Bu, hizmet uygulamasının bir örneğinin DI kapsayıcısına eklenmesini içerir:</span><span class="sxs-lookup"><span data-stu-id="b7a58-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="b7a58-121">Ancak, tek ton ömrü olan bir hizmet uygulaması artık kapsamlı hizmetleri yapıcı enjeksiyon yoluyla çözemez.</span><span class="sxs-lookup"><span data-stu-id="b7a58-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="b7a58-122">gRPC hizmet seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b7a58-122">Configure gRPC services options</span></span>

<span data-ttu-id="b7a58-123">C-core tabanlı uygulamalarda, Sunucu `grpc.max_receive_message_length` `grpc.max_send_message_length` `ChannelOption` [örneğini oluştururken](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)ayarlar gibi ayarlar yapılandırılır ve yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="b7a58-124">ASP.NET Core'da, gRPC `GrpcServiceOptions` türü üzerinden yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7a58-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="b7a58-125">Örneğin, bir gRPC hizmetinin gelen en yüksek ileti boyutu `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="b7a58-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="b7a58-126">Aşağıdaki örnekte, `MaxReceiveMessageSize` 4 MB varsayılanı 16 MB olarak değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b7a58-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="b7a58-127">Yapılandırma hakkında daha fazla <xref:grpc/configuration>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="b7a58-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="b7a58-128">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="b7a58-128">Logging</span></span>

<span data-ttu-id="b7a58-129">C-core tabanlı uygulamalar hata `GrpcEnvironment` ayıklama amacıyla [logger yapılandırmak](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) için güveniyor.</span><span class="sxs-lookup"><span data-stu-id="b7a58-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="b7a58-130">ASP.NET Çekirdek yığını bu işlevselliği [Günlük API'sı](xref:fundamentals/logging/index)aracılığıyla sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7a58-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b7a58-131">Örneğin, bir logger oluşturucu enjeksiyon yoluyla gRPC hizmetine eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="b7a58-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="b7a58-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b7a58-132">HTTPS</span></span>

<span data-ttu-id="b7a58-133">C-core tabanlı [uygulamalar, HTTPS'yi Server.Ports özelliği](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)üzerinden yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b7a58-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="b7a58-134">Benzer bir kavram ASP.NET Core sunucuları yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="b7a58-135">Örneğin, Kestrel bu işlevsellik için [uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) kullanır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="b7a58-136">gRPC Interceptors vs Middleware</span><span class="sxs-lookup"><span data-stu-id="b7a58-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="b7a58-137">ASP.NET Core [ara yazılım,](xref:fundamentals/middleware/index) C-core tabanlı gRPC uygulamalarındaki önleyicilere kıyasla benzer işlevler sunar.</span><span class="sxs-lookup"><span data-stu-id="b7a58-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="b7a58-138">ASP.NET Core ara yazılım ları ve önleyiciler kavramsal olarak benzerdir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="b7a58-139">Hem:</span><span class="sxs-lookup"><span data-stu-id="b7a58-139">Both:</span></span>

* <span data-ttu-id="b7a58-140">GRPC isteğini işleyen bir ardışık hatlar oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="b7a58-141">Çalışma, ardışık işlemdeki bir sonraki bileşenden önce veya sonra yapılmasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="b7a58-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="b7a58-142">Şuna `HttpContext`erişim sağlayın:</span><span class="sxs-lookup"><span data-stu-id="b7a58-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="b7a58-143">Middleware bir `HttpContext` parametredir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="b7a58-144">Önleyicilerde `HttpContext` `ServerCallContext` `ServerCallContext.GetHttpContext` uzantı yöntemi ile parametre kullanılarak erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="b7a58-145">Bu özelliğin ASP.NET Core'da çalışan önleyicilere özel olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="b7a58-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="b7a58-146">ASP.NET Core Middleware gRPC Interceptor farklılıklar:</span><span class="sxs-lookup"><span data-stu-id="b7a58-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="b7a58-147">Interceptors:</span><span class="sxs-lookup"><span data-stu-id="b7a58-147">Interceptors:</span></span>
  * <span data-ttu-id="b7a58-148">[ServerCallContext'ı](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)kullanarak gRPC soyutlama katmanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="b7a58-149">Şuna erişim sağlayın:</span><span class="sxs-lookup"><span data-stu-id="b7a58-149">Provide access to:</span></span>
    * <span data-ttu-id="b7a58-150">Bir çağrıya gönderilen deserialized ileti.</span><span class="sxs-lookup"><span data-stu-id="b7a58-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="b7a58-151">İleti seri hale getirilmeden önce aramadan döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b7a58-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="b7a58-152">gRPC hizmetlerinden atılan özel durumları yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="b7a58-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="b7a58-153">Middleware:</span></span>
  * <span data-ttu-id="b7a58-154">gRPC avcılarından önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="b7a58-155">Altta yatan HTTP/2 iletileri üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="b7a58-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="b7a58-156">Yalnızca istek ve yanıt akışlarından baytlara erişebilir.</span><span class="sxs-lookup"><span data-stu-id="b7a58-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7a58-157">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b7a58-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
