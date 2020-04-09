---
title: ASP.NET Core için gRPC'de güvenlik hususları
author: jamesnk
description: ASP.NET Core için gRPC için güvenlik konuları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667323"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC'de güvenlik hususları

Yazar: [James Newton-King](https://twitter.com/jamesnk)

Bu makalede, .NET Core ile gRPC güvenliğini sağlama hakkında bilgi sağlar.

## <a name="transport-security"></a>Ulaşım güvenliği

gRPC mesajları HTTP/2 kullanılarak gönderilir ve alınır. Şunları öneririz:

* [Taşıma Katmanı Güvenliği (TLS),](https://tools.ietf.org/html/rfc5246) üretim gRPC uygulamalarındaki iletileri güvenli hale getirmek için kullanılır.
* gRPC hizmetleri yalnızca güvenli bağlantı noktaları üzerinden dinlemeli ve yanıt vermelidir.

TLS Kerkenez'de yapılandırılır. Kestrel uç noktalarının yapılandırılması hakkında daha fazla bilgi için [Kerkenez uç noktası](xref:fundamentals/servers/kestrel#endpoint-configuration)yapılandırması'na bakın.

## <a name="exceptions"></a>Özel Durumlar

Özel durum iletileri genellikle istemciye açıklanmaması gereken hassas veriler olarak kabul edilir. Varsayılan olarak, gRPC istemciye bir gRPC hizmeti tarafından atılan bir özel durum ayrıntılarını göndermez. Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır. İstemciye özel durum iletisi [teslimi, EnableDetailedErrors](xref:grpc/configuration#configure-services-options)ile (örneğin, geliştirme veya testte) geçersiz kılınabilir. Özel durum iletileri üretim uygulamalarında istemciye maruz kalmamalıdır.

## <a name="message-size-limits"></a>İleti boyutu sınırları

gRPC istemcilerine ve hizmetlerine gelen iletiler belleğe yüklenir. İleti boyutu sınırları, gRPC'nin aşırı kaynak tüketmesini önlemeye yardımcı olan bir mekanizmadır.

gRPC gelen ve giden iletileri yönetmek için ileti başına boyut sınırları kullanır. Varsayılan olarak, gRPC gelen iletileri 4 MB ile sınırlar. Giden iletilerde sınır yoktur.

Sunucuda, gRPC ileti limitleri aşağıdaki bir uygulamadaki `AddGrpc`tüm hizmetler için yapılandırılabilir:

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

Limitler, tek bir hizmet için `AddServiceOptions<TService>`de yapılandırılabilir. İleti boyutu sınırlarını yapılandırma hakkında daha fazla bilgi için [gRPC yapılandırması'na](xref:grpc/configuration)bakın.

## <a name="client-certificate-validation"></a>İstemci sertifikası doğrulama

[Bağlantı](https://tools.ietf.org/html/rfc5246#section-7.4.4) kurulduğunda istemci sertifikaları başlangıçta doğrulanır. Varsayılan olarak, Kestrel bir bağlantıistemci sertifikasıek doğrulama gerçekleştirmez.

İstemci sertifikaları tarafından güvence altına verilen gRPC hizmetlerinin [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) paketini kullanmasını öneririz. ASP.NET Core sertifika kimlik doğrulaması, istemci sertifikasında şunları dahil olmak üzere ek doğrulama gerçekleştirecektir:

* Sertifikanın geçerli bir genişletilmiş anahtar kullanımı (EKU) vardır
* Geçerlilik süresi içinde
* Sertifika iptalini denetle
