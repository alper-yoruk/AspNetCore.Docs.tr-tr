---
title: .NET yapılandırması için gRPC
author: jamesnk
description: .NET uygulamaları için gRPC'yi nasıl yapılandırıştırabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: 4c13c45ce745643c3cb089a1c984d2ef599db48b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440837"
---
# <a name="grpc-for-net-configuration"></a>.NET yapılandırması için gRPC

## <a name="configure-services-options"></a>Hizmet seçeneklerini yapılandırma

gRPC hizmetleri *Startup.cs* `AddGrpc` ile yapılandırılır. Aşağıdaki tabloda gRPC hizmetlerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Sunucudan gönderilebilen baytlarda en yüksek ileti boyutu. Yapılandırılan maksimum ileti boyutunu aşan bir ileti göndermeye çalışmak bir özel durumla sonuçlanır. `null`Ayarlandığında, ileti boyutu sınırsızdır. |
| MaxReceiveMessageSize | 4 MB | Sunucu tarafından alınabilecek baytlarda en yüksek ileti boyutu. Sunucu bu sınırı aşan bir ileti alırsa, bir özel durum atar. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. `null`Ayarlandığında, ileti boyutu sınırsızdır. |
| Ayrıntılı Hataları Etkinleştirme | `false` | Bir `true`hizmet yönteminde bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse. Varsayılan değer: `false`. Ayar `EnableDetailedErrors` `true` hassas bilgileri sızdırabilir. |
| Sıkıştırma Sağlayıcıları | Gzip | İletileri sıkıştırmak ve sıkıştırmak için kullanılan sıkıştırma sağlayıcıları topluluğu. Özel sıkıştırma sağlayıcıları oluşturulabilir ve koleksiyona eklenebilir. Varsayılan yapılandırılan sağlayıcılar **gzip** sıkıştırmayı destekler. |
| <span style="word-break:normal;word-wrap:normal">YanıtSıkıştırma Algoritması</span> | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma algoritması. Algoritma, ''deki bir `CompressionProviders`sıkıştırma sağlayıcısıyla eşleşmelidir. Algoritmanın yanıtı sıkıştırması için istemcinin algoritmayı **grpc-accept-coding** üstbilgisine göndererek desteklediğini göstermesi gerekir. |
| YanıtSıkıştırma Düzeyi | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma düzeyi. |
| Durdurucular | Hiçbiri | Her gRPC çağrısıyla çalıştırılabilen bir önleyici koleksiyonu. Durdurucular kayıtlı oldukları sırayla çalıştırılır. Küresel olarak yapılandırılmış durdurucular, tek bir hizmet için yapılandırılan önleyiciler çalıştırılmadan önce çalıştırılır. gRPC önleyiciler hakkında daha fazla bilgi için [gRPC Interceptors vs. Middleware'e](xref:grpc/migration#grpc-interceptors-vs-middleware)bakın. |

Seçenekler tüm hizmetler için `AddGrpc` bir seçenek temsilcisi sunarak `Startup.ConfigureServices`yapılandırılabilir:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Tek bir hizmet için seçenekler, sağlanan `AddGrpc` genel seçenekleri geçersiz `AddServiceOptions<TService>`kılar ve aşağıdakiler kullanılarak yapılandırılabilir:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

gRPC istemci yapılandırması `GrpcChannelOptions`ayarlanır. Aşağıdaki tabloda gRPC kanallarını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| httpİsteC | Yeni örnek | GRPC `HttpClient` aramaları yapmak için kullanılır. İstemci, özel `HttpClientHandler`bir yapıya uygun olarak ayarlanabilir veya gRPC çağrıları için HTTP ardışık hattına ek işleyiciler ekleyebilir. Hayır `HttpClient` belirtilmişse, `HttpClient` kanal için yeni bir örnek oluşturulur. Otomatik olarak imha edilecektir. |
| Elden ÇıkarhttpClient | `false` | `true`Eğer , `HttpClient` ve bir belirtilirse, `HttpClient` o zaman örnek bertaraf edilir. `GrpcChannel` |
| LoggerFactory | `null` | Istemci `LoggerFactory` tarafından gRPC aramaları hakkında bilgi günlüğe kaydetmek için kullanılır. Bir `LoggerFactory` örnek bağımlılık enjeksiyonundan çözülebilir `LoggerFactory.Create`veya . Günlüğe kaydetmeyi yapılandırma <xref:grpc/diagnostics#grpc-client-logging>örnekleri için bkz. |
| MaxSendMessageSize | `null` | İstemciden gönderilebilen baytlarda en büyük ileti boyutu. Yapılandırılan maksimum ileti boyutunu aşan bir ileti göndermeye çalışmak bir özel durumla sonuçlanır. `null`Ayarlandığında, ileti boyutu sınırsızdır. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | İstemci tarafından alınabilecek baytlarda en büyük ileti boyutu. İstemci bu sınırı aşan bir ileti alırsa, bir özel durum atar. Bu değeri artırmak istemcinin daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. `null`Ayarlandığında, ileti boyutu sınırsızdır. |
| Kimlik Bilgileri | `null` | Bir `ChannelCredentials` örnek. Kimlik bilgileri, gRPC çağrılarına kimlik doğrulama meta verileri eklemek için kullanılır. |
| Sıkıştırma Sağlayıcıları | Gzip | İletileri sıkıştırmak ve sıkıştırmak için kullanılan sıkıştırma sağlayıcıları topluluğu. Özel sıkıştırma sağlayıcıları oluşturulabilir ve koleksiyona eklenebilir. Varsayılan yapılandırılan sağlayıcılar **gzip** sıkıştırmayı destekler. |

Aşağıdaki kod:

* Kanaldaki en fazla gönder ve alma ileti boyutunu ayarlar.
* Bir istemci oluşturur.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
