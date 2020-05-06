---
title: .NET için gRPC yapılandırması
author: jamesnk
description: GRPC 'yi .NET uygulamaları için nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: 6e4259b538d32490d5281f189a04ab5a04dbcce5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774697"
---
# <a name="grpc-for-net-configuration"></a>.NET için gRPC yapılandırması

## <a name="configure-services-options"></a>Hizmet seçeneklerini yapılandırma

gRPC Hizmetleri, *Startup.cs*içinde `AddGrpc` ile yapılandırılır. Aşağıdaki tabloda, gRPC hizmetlerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Sunucudan gönderilebilecek en büyük ileti boyutu (bayt). Yapılandırılan en büyük ileti boyutunu aşan bir ileti gönderilmeye çalışılıyor, bir özel durumla sonuçlanır. Olarak `null`ayarlandığında, ileti boyutu sınırsızdır. |
| MaxReceiveMessageSize | 4 MB | Sunucu tarafından alınabilecek, bayt olarak en büyük ileti boyutu. Sunucu bu sınırı aşan bir ileti alırsa bir özel durum oluşturur. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. Olarak `null`ayarlandığında, ileti boyutu sınırsızdır. |
| EnableDetailedErrors | `false` | İse `true`, bir hizmet yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan değer: `false`. İçin `EnableDetailedErrors` `true` ayarı, hassas bilgileri sızdırabilir. |
| CompressionProviders | gzip | İletileri sıkıştırmak ve açmak için kullanılan bir sıkıştırma sağlayıcıları koleksiyonu. Özel sıkıştırma sağlayıcıları oluşturulup koleksiyona eklenebilir. Varsayılan yapılandırılmış sağlayıcılar **gzip** sıkıştırmasını destekler. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma algoritması. Algoritmanın içindeki `CompressionProviders`bir sıkıştırma sağlayıcısıyla eşleşmesi gerekir. Bir yanıtı sıkıştırmaya yönelik algoritma için, istemci, **GRPC-Accept-Encoding** üstbilgisine göndererek algoritmayı desteklediğini göstermelidir. |
| ResponseCompressionLevel | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma düzeyi. |
| Durdurucular | Hiçbiri | Her gRPC çağrısıyla çalıştırılan bir dinleyici koleksiyonu. Yakalayıcılar kayıtlı oldukları sırada çalıştırılır. Küresel olarak yapılandırılan yakalayıcılar, tek bir hizmet için yapılandırmadan önce çalıştırılır. GRPC yakalayıcılar hakkında daha fazla bilgi için bkz. [GRPC yakalayıcılar Ile ara yazılım karşılaştırması](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Seçenekler, `AddGrpc` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm hizmetler için yapılandırılabilir:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Tek bir hizmetin seçenekleri ' de `AddGrpc` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak `AddServiceOptions<TService>`yapılandırılabilir:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

gRPC istemci yapılandırması üzerinde `GrpcChannelOptions`ayarlanır. Aşağıdaki tabloda, gRPC kanallarını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| HttpClient | Yeni örnek | GRPC çağrısı yapmak için `HttpClient` kullanılır. İstemci, özel `HttpClientHandler`yapılandırmak üzere ayarlanabilir veya GRPC ÇAĞRıLARıNA yönelik http işlem hattına ek işleyiciler ekleyebilir. Hayır `HttpClient` belirtilmişse kanal için yeni `HttpClient` bir örnek oluşturulur. Otomatik olarak elden kaldırılacaktır. |
| DisposeHttpClient | `false` | `true`, Ve bir `HttpClient` belirtilmişse,,, bırakıldığında `HttpClient` `GrpcChannel` örnek de silinir. |
| LoggerFactory | `null` | İstemci `LoggerFactory` tarafından GRPC çağrıları hakkındaki bilgileri günlüğe kaydetmek için kullanılır. `LoggerFactory` Örnek, kullanılarak `LoggerFactory.Create`bağımlılık ekleme veya oluşturma öğesinden çözülebilir. Günlüğe kaydetmeyi yapılandırma örnekleri için bkz <xref:grpc/diagnostics#grpc-client-logging>.. |
| MaxSendMessageSize | `null` | İstemciden gönderilebilecek en büyük ileti boyutu (bayt). Yapılandırılan en büyük ileti boyutunu aşan bir ileti gönderilmeye çalışılıyor, bir özel durumla sonuçlanır. Olarak `null`ayarlandığında, ileti boyutu sınırsızdır. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | İstemci tarafından alınabilecek, bayt olarak en büyük ileti boyutu. İstemci bu sınırı aşan bir ileti alırsa bir özel durum oluşturur. Bu değeri artırmak, istemcinin daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. Olarak `null`ayarlandığında, ileti boyutu sınırsızdır. |
| Kimlik Bilgileri | `null` | Bir `ChannelCredentials` örnek. Kimlik bilgileri, gRPC çağrılarına kimlik doğrulama meta verileri eklemek için kullanılır. |
| CompressionProviders | gzip | İletileri sıkıştırmak ve açmak için kullanılan bir sıkıştırma sağlayıcıları koleksiyonu. Özel sıkıştırma sağlayıcıları oluşturulup koleksiyona eklenebilir. Varsayılan yapılandırılmış sağlayıcılar **gzip** sıkıştırmasını destekler. |

Aşağıdaki kod:

* Kanalda en büyük gönderme ve alma iletisi boyutunu ayarlar.
* İstemci oluşturur.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
