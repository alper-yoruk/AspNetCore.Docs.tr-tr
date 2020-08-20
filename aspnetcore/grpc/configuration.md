---
title: .NET için gRPC yapılandırması
author: jamesnk
description: GRPC 'yi .NET uygulamaları için nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: 8a4f518e30432a79151ec34a7092123c390f4d5d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631153"
---
# <a name="grpc-for-net-configuration"></a>.NET için gRPC yapılandırması

## <a name="configure-services-options"></a>Hizmet seçeneklerini yapılandırma

gRPC Hizmetleri, `AddGrpc` *Startup.cs*içinde ile yapılandırılır. Aşağıdaki tabloda, gRPC hizmetlerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Sunucudan gönderilebilecek en büyük ileti boyutu (bayt). Yapılandırılan en büyük ileti boyutunu aşan bir ileti gönderilmeye çalışılıyor, bir özel durumla sonuçlanır. Olarak ayarlandığında `null` , ileti boyutu sınırsızdır. |
| MaxReceiveMessageSize | 4 MB | Sunucu tarafından alınabilecek, bayt olarak en büyük ileti boyutu. Sunucu bu sınırı aşan bir ileti alırsa bir özel durum oluşturur. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. Olarak ayarlandığında `null` , ileti boyutu sınırsızdır. |
| EnableDetailedErrors | `false` | İse `true` , bir hizmet yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan değer: `false`. `EnableDetailedErrors`İçin ayarı `true` , hassas bilgileri sızdırabilir. |
| CompressionProviders | gzip | İletileri sıkıştırmak ve açmak için kullanılan bir sıkıştırma sağlayıcıları koleksiyonu. Özel sıkıştırma sağlayıcıları oluşturulup koleksiyona eklenebilir. Varsayılan yapılandırılmış sağlayıcılar **gzip** sıkıştırmasını destekler. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma algoritması. Algoritmanın içindeki bir sıkıştırma sağlayıcısıyla eşleşmesi gerekir `CompressionProviders` . Bir yanıtı sıkıştırmaya yönelik algoritma için, istemci, **GRPC-Accept-Encoding** üstbilgisine göndererek algoritmayı desteklediğini göstermelidir. |
| ResponseCompressionLevel | `null` | Sunucudan gönderilen iletileri sıkıştırmak için kullanılan sıkıştırma düzeyi. |
| Durdurucular | Yok | Her gRPC çağrısıyla çalıştırılan bir dinleyici koleksiyonu. Yakalayıcılar kayıtlı oldukları sırada çalıştırılır. Küresel olarak yapılandırılan yakalayıcılar, tek bir hizmet için yapılandırmadan önce çalıştırılır. GRPC yakalayıcılar hakkında daha fazla bilgi için bkz. [GRPC yakalayıcılar Ile ara yazılım karşılaştırması](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| Ignoreunknownservices | `false` | `true`, Bilinmeyen hizmetlere ve yöntemlere yapılan çağrılar **uygulanmayan** bir durum döndürmez ve istek ASP.NET Core sonraki kayıtlı ara yazılıma geçer. |

Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm hizmetler için yapılandırılabilir `AddGrpc` `Startup.ConfigureServices` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Tek bir hizmetin seçenekleri ' de belirtilen genel seçenekleri geçersiz kılar `AddGrpc` ve kullanılarak yapılandırılabilir `AddServiceOptions<TService>` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

gRPC istemci yapılandırması üzerinde ayarlanır `GrpcChannelOptions` . Aşağıdaki tabloda, gRPC kanallarını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| HttpHandler | Yeni örnek | `HttpMessageHandler`GRPC çağrısı yapmak için kullanılır. İstemci `HttpClientHandler` , gRPC çağrılarına YÖNELIK http işlem hattına özel bir yapılandırma veya ek işleyiciler ekleme şeklinde ayarlanabilir. Hayır `HttpMessageHandler` belirtilirse `HttpClientHandler` kanal için otomatik elden çıkarmada yeni bir örnek oluşturulur. |
| HttpClient | `null` | `HttpClient`GRPC çağrısı yapmak için kullanılır. Bu ayar, için bir alternatiftir `HttpHandler` . |
| DisposeHttpClient | `false` | `true`Ve bir veya belirtilirse,,,,,,,,,, `HttpMessageHandler` `HttpClient` `HttpHandler` `HttpClient` bırakıldığında `GrpcChannel` ,,,,,,,,,,,,, |
| LoggerFactory | `null` | `LoggerFactory`İstemci tarafından gRPC çağrıları hakkındaki bilgileri günlüğe kaydetmek için kullanılır. `LoggerFactory`Örnek, kullanılarak bağımlılık ekleme veya oluşturma öğesinden çözülebilir `LoggerFactory.Create` . Günlüğe kaydetmeyi yapılandırma örnekleri için bkz <xref:grpc/diagnostics#grpc-client-logging> .. |
| MaxSendMessageSize | `null` | İstemciden gönderilebilecek en büyük ileti boyutu (bayt). Yapılandırılan en büyük ileti boyutunu aşan bir ileti gönderilmeye çalışılıyor, bir özel durumla sonuçlanır. Olarak ayarlandığında `null` , ileti boyutu sınırsızdır. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | İstemci tarafından alınabilecek, bayt olarak en büyük ileti boyutu. İstemci bu sınırı aşan bir ileti alırsa bir özel durum oluşturur. Bu değeri artırmak, istemcinin daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. Olarak ayarlandığında `null` , ileti boyutu sınırsızdır. |
| Kimlik bilgileri | `null` | Bir `ChannelCredentials` örnek. Kimlik bilgileri, gRPC çağrılarına kimlik doğrulama meta verileri eklemek için kullanılır. |
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
