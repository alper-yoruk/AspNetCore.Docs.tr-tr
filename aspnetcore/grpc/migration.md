---
title: GRPC hizmetlerini C Core 'dan ASP.NET Core geçirme
author: juntaoluo
description: Mevcut bir C çekirdekli tabanlı gRPC uygulamasını ASP.NET Core yığının üstünde çalışacak şekilde taşımayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/migration
ms.openlocfilehash: 1846195cc43aec703333e69f66380ddcabcf2ad4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768828"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="8e360-103">GRPC hizmetlerini C Core 'dan ASP.NET Core geçirme</span><span class="sxs-lookup"><span data-stu-id="8e360-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="8e360-104">[John Luo](https://github.com/juntaoluo) tarafından</span><span class="sxs-lookup"><span data-stu-id="8e360-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="8e360-105">Temel alınan yığının uygulanması nedeniyle, tüm özellikler [C Core tabanlı GRPC](https://grpc.io/blog/grpc-stacks) uygulamaları ve ASP.NET Core tabanlı uygulamalar arasında aynı şekilde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8e360-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="8e360-106">Bu belgede, iki yığın arasında geçiş yapmak için önemli farklılıklar vurgulanmıştır.</span><span class="sxs-lookup"><span data-stu-id="8e360-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="8e360-107">gRPC hizmeti uygulama ömrü</span><span class="sxs-lookup"><span data-stu-id="8e360-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="8e360-108">ASP.NET Core yığınında, varsayılan olarak gRPC Hizmetleri [kapsamlı bir ömür](xref:fundamentals/dependency-injection#service-lifetimes)ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8e360-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8e360-109">Buna karşılık, gRPC C-Core varsayılan olarak [tek bir yaşam süresine](xref:fundamentals/dependency-injection#service-lifetimes)sahip bir hizmete bağlanır.</span><span class="sxs-lookup"><span data-stu-id="8e360-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="8e360-110">Kapsamlı ömür, hizmet uygulamasının kapsamlı ömürlerle diğer hizmetleri çözümlemesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="8e360-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="8e360-111">Örneğin, kapsamlı bir yaşam süresi aynı zamanda, `DbContext` Oluşturucu ekleme yoluyla dı kapsayıcısından da çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="8e360-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="8e360-112">Kapsamlı ömür kullanımı:</span><span class="sxs-lookup"><span data-stu-id="8e360-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="8e360-113">Her istek için hizmet uygulamasının yeni bir örneği oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8e360-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="8e360-114">Uygulama türündeki örnek üyeleri aracılığıyla istekler arasında durum paylaşmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="8e360-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="8e360-115">Beklentisi, paylaşılan durumları dı kapsayıcısında tek bir hizmette depobir biçimde depokadır.</span><span class="sxs-lookup"><span data-stu-id="8e360-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="8e360-116">Depolanan paylaşılan durumlar, gRPC hizmet uygulamasının oluşturucusunda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="8e360-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="8e360-117">Hizmet yaşam süreleri hakkında daha fazla bilgi için <xref:fundamentals/dependency-injection#service-lifetimes>bkz..</span><span class="sxs-lookup"><span data-stu-id="8e360-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="8e360-118">Tek bir hizmet ekleyin</span><span class="sxs-lookup"><span data-stu-id="8e360-118">Add a singleton service</span></span>

<span data-ttu-id="8e360-119">GRPC C çekirdekli bir uygulamadan ASP.NET Core geçişi kolaylaştırmak için, hizmet uygulamasının hizmet ömrünü kapsamlı olarak tek başına değiştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="8e360-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="8e360-120">Bu, bir hizmet uygulamasının bir örneğini dı kapsayıcısına eklemeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="8e360-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="8e360-121">Ancak, tek bir yaşam süresine sahip bir hizmet uygulamasının kapsamı, kapsamlı hizmetleri Oluşturucu ekleme yoluyla çözemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="8e360-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="8e360-122">GRPC Hizmetleri seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8e360-122">Configure gRPC services options</span></span>

<span data-ttu-id="8e360-123">C Core tabanlı uygulamalarda `grpc.max_receive_message_length` , ve `grpc.max_send_message_length` gibi ayarlar `ChannelOption` [sunucu örneği oluşturulurken](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="8e360-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="8e360-124">ASP.NET Core, gRPC, `GrpcServiceOptions` tür aracılığıyla yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="8e360-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="8e360-125">Örneğin, gRPC hizmetinin en büyük gelen ileti boyutu ile `AddGrpc`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8e360-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="8e360-126">Aşağıdaki örnek 4 MB ile 16 `MaxReceiveMessageSize` MB arasında varsayılan değer değiştirir:</span><span class="sxs-lookup"><span data-stu-id="8e360-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="8e360-127">Yapılandırma hakkında daha fazla bilgi için bkz <xref:grpc/configuration>..</span><span class="sxs-lookup"><span data-stu-id="8e360-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="8e360-128">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="8e360-128">Logging</span></span>

<span data-ttu-id="8e360-129">C çekirdekli tabanlı uygulamalar, `GrpcEnvironment` hata ayıklama amacıyla [günlükçüsü yapılandırmak](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) için kullanır.</span><span class="sxs-lookup"><span data-stu-id="8e360-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="8e360-130">ASP.NET Core Stack, bu işlevselliği [günlüğe kaydetme API 'si](xref:fundamentals/logging/index)aracılığıyla sağlar.</span><span class="sxs-lookup"><span data-stu-id="8e360-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8e360-131">Örneğin, gRPC hizmetine Oluşturucu ekleme yoluyla bir günlükçü eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="8e360-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="8e360-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8e360-132">HTTPS</span></span>

<span data-ttu-id="8e360-133">C-Core tabanlı uygulamalar, [Server. Ports özelliği](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)aracılığıyla https 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="8e360-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="8e360-134">Benzer bir kavram, ASP.NET Core sunucuları yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8e360-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="8e360-135">Örneğin, Kestrel Bu işlevsellik için [uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) kullanır.</span><span class="sxs-lookup"><span data-stu-id="8e360-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="8e360-136">gRPC yakalayıcılar vs ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="8e360-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="8e360-137">ASP.NET Core [Ara yazılım](xref:fundamentals/middleware/index) , C çekirdekli tabanlı GRPC uygulamalarındaki yakalayıcılar ile karşılaştırıldığında benzer işlevler sunar.</span><span class="sxs-lookup"><span data-stu-id="8e360-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="8e360-138">ASP.NET Core ara yazılımı ve yakalayıcılar kavramsal olarak benzerdir.</span><span class="sxs-lookup"><span data-stu-id="8e360-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="8e360-139">İs</span><span class="sxs-lookup"><span data-stu-id="8e360-139">Both:</span></span>

* <span data-ttu-id="8e360-140">, Bir gRPC isteğini işleyen bir işlem hattı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8e360-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="8e360-141">İşlem hattındaki bir sonraki bileşenden önce veya sonra iş gerçekleştirilmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="8e360-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="8e360-142">Erişim sağla `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="8e360-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="8e360-143">Ara yazılım `HttpContext` ' de bir parametredir.</span><span class="sxs-lookup"><span data-stu-id="8e360-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="8e360-144">Yakalayıcılar içinde, `HttpContext` `ServerCallContext` `ServerCallContext.GetHttpContext` uzantı yöntemiyle parametresi kullanılarak erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="8e360-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="8e360-145">Bu özelliğin ASP.NET Core ' de çalışan yakalayıcılar için özel olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="8e360-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="8e360-146">gRPC yakalayıcısı ASP.NET Core ara yazılım farklılıkları:</span><span class="sxs-lookup"><span data-stu-id="8e360-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="8e360-147">Kesiciler</span><span class="sxs-lookup"><span data-stu-id="8e360-147">Interceptors:</span></span>
  * <span data-ttu-id="8e360-148">[Servercallcontext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)kullanarak GRPC soyutlama katmanı üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8e360-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="8e360-149">Erişim sağla:</span><span class="sxs-lookup"><span data-stu-id="8e360-149">Provide access to:</span></span>
    * <span data-ttu-id="8e360-150">Seri durumdan çıkarılmış ileti bir çağrıya gönderildi.</span><span class="sxs-lookup"><span data-stu-id="8e360-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="8e360-151">Seri hale getirilmeden önce çağrıdan döndürülen ileti.</span><span class="sxs-lookup"><span data-stu-id="8e360-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="8e360-152">, GRPC hizmetlerinden oluşturulan özel durumları yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8e360-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="8e360-153">Yazılımlar</span><span class="sxs-lookup"><span data-stu-id="8e360-153">Middleware:</span></span>
  * <span data-ttu-id="8e360-154">GRPC yakalayıcılar öncesinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8e360-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="8e360-155">Temel alınan HTTP/2 iletileri üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8e360-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="8e360-156">Yalnızca istek ve yanıt akışlarından gelen baytlara erişebilir.</span><span class="sxs-lookup"><span data-stu-id="8e360-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e360-157">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8e360-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
