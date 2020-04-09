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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>GRPC hizmetlerini C-core'dan ASP.NET Core'a geçirme

Yazar: [John Luo](https://github.com/juntaoluo)

Temel yığının uygulanması nedeniyle, tüm özellikler [C-core tabanlı gRPC](https://grpc.io/blog/grpc-stacks) uygulamaları ile ASP.NET Çekirdek tabanlı uygulamalar arasında aynı şekilde çalışmaz. Bu belge, iki yığın arasında geçiş için önemli farkları vurgular.

## <a name="grpc-service-implementation-lifetime"></a>gRPC hizmet uygulama ömrü

ASP.NET Çekirdek yığınında, gRPC hizmetleri varsayılan olarak [kapsamlı](xref:fundamentals/dependency-injection#service-lifetimes)bir ömür boyu ile oluşturulur. Buna karşılık, gRPC C-core varsayılan olarak [tekton ömrü](xref:fundamentals/dependency-injection#service-lifetimes)olan bir hizmete bağlanır.

Kapsamlı bir ömür, hizmet uygulamasının kapsamlı yaşam sürelerine sahip diğer hizmetleri çözmesine olanak tanır. Örneğin, kapsamlı bir ömür, `DbContext` DI kabından yapıcı enjeksiyon yoluyla da çözülebilir. Kapsamlı kullanım ömrünü kullanma:

* Her istek için hizmet uygulamasının yeni bir örneği oluşturulur.
* Uygulama türünde örnek üyeler aracılığıyla istekler arasında durum paylaşımı mümkün değildir.
* Beklenti, paylaşılan durumları DI kapsayıcısında ki tek tonluk bir hizmette depolamaktır. Depolanan paylaşılan durumlar gRPC hizmet uygulamasının oluşturucusunda çözülür.

Hizmet yaşam süreleri hakkında <xref:fundamentals/dependency-injection#service-lifetimes>daha fazla bilgi için bkz.

### <a name="add-a-singleton-service"></a>Singleton hizmeti ekleme

gRPC C-core uygulamasından ASP.NET Core'a geçişi kolaylaştırmak için, hizmet uygulamasının hizmet ömrünü tek ton kapsamından değiştirmek mümkündür. Bu, hizmet uygulamasının bir örneğinin DI kapsayıcısına eklenmesini içerir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Ancak, tek ton ömrü olan bir hizmet uygulaması artık kapsamlı hizmetleri yapıcı enjeksiyon yoluyla çözemez.

## <a name="configure-grpc-services-options"></a>gRPC hizmet seçeneklerini yapılandırma

C-core tabanlı uygulamalarda, Sunucu `grpc.max_receive_message_length` `grpc.max_send_message_length` `ChannelOption` [örneğini oluştururken](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)ayarlar gibi ayarlar yapılandırılır ve yapılandırılır.

ASP.NET Core'da, gRPC `GrpcServiceOptions` türü üzerinden yapılandırma sağlar. Örneğin, bir gRPC hizmetinin gelen en yüksek ileti boyutu `AddGrpc`. Aşağıdaki örnekte, `MaxReceiveMessageSize` 4 MB varsayılanı 16 MB olarak değiştirin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Yapılandırma hakkında daha fazla <xref:grpc/configuration>bilgi için bkz.

## <a name="logging"></a>Günlüğe kaydetme

C-core tabanlı uygulamalar hata `GrpcEnvironment` ayıklama amacıyla [logger yapılandırmak](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) için güveniyor. ASP.NET Çekirdek yığını bu işlevselliği [Günlük API'sı](xref:fundamentals/logging/index)aracılığıyla sağlar. Örneğin, bir logger oluşturucu enjeksiyon yoluyla gRPC hizmetine eklenebilir:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C-core tabanlı [uygulamalar, HTTPS'yi Server.Ports özelliği](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)üzerinden yapılandırın. Benzer bir kavram ASP.NET Core sunucuları yapılandırmak için kullanılır. Örneğin, Kestrel bu işlevsellik için [uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) kullanır.

## <a name="grpc-interceptors-vs-middleware"></a>gRPC Interceptors vs Middleware

ASP.NET Core [ara yazılım,](xref:fundamentals/middleware/index) C-core tabanlı gRPC uygulamalarındaki önleyicilere kıyasla benzer işlevler sunar. ASP.NET Core ara yazılım ları ve önleyiciler kavramsal olarak benzerdir. Hem:

* GRPC isteğini işleyen bir ardışık hatlar oluşturmak için kullanılır.
* Çalışma, ardışık işlemdeki bir sonraki bileşenden önce veya sonra yapılmasına izin verin.
* Şuna `HttpContext`erişim sağlayın:
  * Middleware bir `HttpContext` parametredir.
  * Önleyicilerde `HttpContext` `ServerCallContext` `ServerCallContext.GetHttpContext` uzantı yöntemi ile parametre kullanılarak erişilebilir. Bu özelliğin ASP.NET Core'da çalışan önleyicilere özel olduğunu unutmayın.

ASP.NET Core Middleware gRPC Interceptor farklılıklar:

* Interceptors:
  * [ServerCallContext'ı](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)kullanarak gRPC soyutlama katmanında çalışır.
  * Şuna erişim sağlayın:
    * Bir çağrıya gönderilen deserialized ileti.
    * İleti seri hale getirilmeden önce aramadan döndürülür.
  * gRPC hizmetlerinden atılan özel durumları yakalayabilir ve işleyebilir.
* Middleware:
  * gRPC avcılarından önce çalışır.
  * Altta yatan HTTP/2 iletileri üzerinde çalışır.
  * Yalnızca istek ve yanıt akışlarından baytlara erişebilir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
