---
title: ASP.NET Core için gRPC 'de güvenlik konuları
author: jamesnk
description: ASP.NET Core için gRPC güvenlik konuları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/security
ms.openlocfilehash: f06e239054b1c4edf126d1cf974dff1ca36ee56a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400134"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC 'de güvenlik konuları

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu makalede, gRPC 'yi .NET Core ile güvenli hale getirme hakkında bilgi verilmektedir.

## <a name="transport-security"></a>Taşıma güvenliği

gRPC iletileri HTTP/2 kullanılarak gönderilir ve alınır. Şunları öneririz:

* [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246) , üretim GRPC uygulamalarında iletileri güvenli hale getirmek için kullanılır.
* gRPC Hizmetleri yalnızca güvenli bağlantı noktalarını dinler ve bunlara yanıt vermelidir.

TLS, Kestrel içinde yapılandırılır. Kestrel uç noktalarını yapılandırma hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Özel durumlar

Özel durum iletileri genellikle bir istemciye görüntülenmemelidir gizli veriler olarak değerlendirilir. Varsayılan olarak, gRPC, bir gRPC hizmeti tarafından oluşturulan özel durumun ayrıntılarını istemciye göndermez. Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır. İstemciye özel durum iletisi teslimi, [Enabledetailederrors](xref:grpc/configuration#configure-services-options)ile geçersiz kılınabilir (örneğin, geliştirme veya test). Özel durum iletileri, üretim uygulamalarındaki istemciye gösterilmemelidir.

## <a name="message-size-limits"></a>İleti boyutu sınırları

GRPC istemcilerine ve hizmetlerine gelen iletiler belleğe yüklenir. İleti boyutu sınırları, gRPC 'nin aşırı kaynak tüketmesini önlemeye yardımcı olmak için bir mekanizmadır.

gRPC gelen ve giden iletileri yönetmek için ileti başına boyut sınırlarını kullanır. Varsayılan olarak, gRPC gelen iletileri 4 MB ile sınırlandırır. Giden iletilerde sınır yoktur.

Sunucusunda, gRPC ileti limitleri bir uygulamadaki tüm hizmetler için ile yapılandırılabilir `AddGrpc` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Ayrıca, kullanılarak tek bir hizmet için sınırlamalar da yapılandırılabilir `AddServiceOptions<TService>` . İleti boyutu sınırlarını yapılandırma hakkında daha fazla bilgi için bkz. [GRPC yapılandırması](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>İstemci sertifikası doğrulaması

[İstemci sertifikaları](https://tools.ietf.org/html/rfc5246#section-7.4.4) , bağlantı oluşturulduğunda başlangıçta onaylanır. Varsayılan olarak, Kestrel bir bağlantının istemci sertifikası için ek doğrulama gerçekleştirmez.

İstemci sertifikaları tarafından güvenliği sağlanan gRPC hizmetlerinin [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) paketini kullanması önerilir. ASP.NET Core sertifika kimlik doğrulaması, bir istemci sertifikasında aşağıdakiler de dahil olmak üzere ek doğrulama gerçekleştirir:

* Sertifikada geçerli bir genişletilmiş anahtar kullanımı (EKU) vardır
* Geçerlilik süresi içinde
* Sertifika iptalini denetle
