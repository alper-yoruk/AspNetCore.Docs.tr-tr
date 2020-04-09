---
title: gRPC hizmetlerini HTTP API’leriyle karşılaştırma
author: jamesnk
description: gRPC'nin HTTP API'lerle nasıl karşılaştırDığını ve senaryoların ne olduğunu öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405877"
---
# <a name="compare-grpc-services-with-http-apis"></a>gRPC hizmetlerini HTTP API’leriyle karşılaştırma

Yazar: [James Newton-King](https://twitter.com/jamesnk)

Bu makalede, [gRPC hizmetlerinin](https://grpc.io/docs/guides/) HTTP API'leri ile (ASP.NET Çekirdek [web API'leri](xref:web-api/index)dahil) karşılaştırılması açıklanmaktadır. Uygulamanız için API sağlamak için kullanılan teknoloji önemli bir seçimdir ve gRPC HTTP API'lere kıyasla benzersiz avantajlar sunar. Bu makalede, gRPC'nin güçlü ve zayıf yönleri tartışılır ve gRPC'yi diğer teknolojilerüzerinde kullanmak için senaryolar önerir.

## <a name="high-level-comparison"></a>Üst düzey karşılaştırma

Aşağıdaki tablo, gRPC ve HTTP API'leri ile JSON arasındaki özelliklerin üst düzey bir karşılaştırmasını sunmaktadır.

| Özellik          | gRPC                                               | JSON ile HTTP API'ler           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Sözleşme         | Gerekli (*.proto*)                                | İsteğe bağlı (OpenAPI)            |
| Protokol         | HTTP/2                                             | HTTP                          |
| Yükü          | [Protobuf (küçük, ikili)](#performance)           | JSON (büyük, insan okunabilir)  |
| Ön yazı | [Sıkı belirtim](#strict-specification)      | Gevşek. Herhangi bir HTTP geçerlidir.     |
| Akış        | [İstemci, sunucu, çift yönlü](#streaming)       | İstemci, sunucu                |
| Tarayıcı desteği  | [Hayır (grpc-web gerektirir)](#limited-browser-support) | Evet                           |
| Güvenlik         | Ulaşım (TLS)                                    | Ulaşım (TLS)               |
| İstemci kodu oluşturma | [Evet](#code-generation)                      | OpenAPI + üçüncü taraf takım |

## <a name="grpc-strengths"></a>gRPC güçlü

### <a name="performance"></a>Performans

gRPC iletileri [protobuf](https://developers.google.com/protocol-buffers/docs/overview)kullanılarak seri hale getirilmiştir, verimli bir ikili ileti biçimi. Protobuf sunucu ve istemci üzerinde çok hızlı bir şekilde serihale getirir. Protobuf serileştirme, mobil uygulamalar gibi sınırlı bant genişliği senaryolarında önemli olan küçük ileti yüklerine neden olabilir.

gRPC HTTP / 2, HTTP 1.x üzerinde önemli performans yararları sağlayan HTTP büyük bir revizyon için tasarlanmıştır:

* İkili çerçeveleme ve sıkıştırma. HTTP/2 protokolü hem gönderme hem de alma konusunda kompakt ve verimlidir.
* Tek bir TCP bağlantısı üzerinden birden fazla HTTP/2 çağrısının çokluğu. [Çoklama, satır başı engellemeyi](https://en.wikipedia.org/wiki/Head-of-line_blocking)ortadan kaldırır.

### <a name="code-generation"></a>Kod oluşturma

Tüm gRPC çerçeveleri kod oluşturma için birinci sınıf destek sağlar. gRPC geliştirme için temel bir dosya gRPC hizmetleri ve iletileri sözleşme tanımlayan [.proto dosyasıdır.](https://developers.google.com/protocol-buffers/docs/proto3) Bu dosyadan gRPC frameworks kod bir hizmet taban sınıfı, iletiler ve tam bir istemci oluşturur.

Sunucu ve istemci arasında *.proto* dosyası paylaşılarak, iletiler ve istemci kodu uçtan uca oluşturulabilir. İstemcinin kod oluşturma istemci ve sunucudaki iletilerin çoğaltılmasını ortadan kaldırır ve sizin için güçlü bir şekilde yazılan bir istemci oluşturur. İstemci yazmak zorunda kalmamak, birçok hizmetin olduğu uygulamalarda önemli geliştirme süresi tasarrufu sağlar.

### <a name="strict-specification"></a>Sıkı belirtim

JSON ile HTTP API için resmi bir belirtim yoktur. Geliştiriciler URL'lerin, HTTP fiillerinin ve yanıt kodlarının en iyi biçimini tartışır.

[gRPC belirtimi,](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) bir gRPC hizmetinin izlemesi gereken biçim hakkında açıklayıcıdır. gRPC tartışmayı ortadan kaldırır ve geliştirici zaman kazandırır, çünkü gRPC platformlar ve uygulamalar arasında tutarlıdır.

### <a name="streaming"></a>Akış

HTTP/2, uzun ömürlü, gerçek zamanlı iletişim akışları için bir temel sağlar. gRPC, HTTP/2 üzerinden akış için birinci sınıf destek sağlar.

bir gRPC hizmeti tüm akış kombinasyonlarını destekler:

* Unary (akış yok)
* Sunucudan istemciakışına
* İstemciden sunucu akışına
* Çift yönlü akış

### <a name="deadlinetimeouts-and-cancellation"></a>Son tarih/zaman ları ve iptal

gRPC, istemcilerin bir RPC'nin tamamlanmasını ne kadar süre yle beklemeye istekli olduklarını belirtmelerine olanak tanır. [Son tarih](https://grpc.io/blog/deadlines) sunucuya gönderilir ve sunucu son tarihi aşarsa hangi eylemin yapılacağına karar verebilir. Örneğin, sunucu zaman ayarı için devam eden gRPC/HTTP/veritabanı isteklerini iptal edebilir.

Son tarihin yayılması ve çocuk gRPC çağrıları aracılığıyla iptal i

## <a name="grpc-recommended-scenarios"></a>gRPC önerilen senaryolar

gRPC aşağıdaki senaryolara uygundur:

* **Microservices** &ndash; gRPC düşük gecikme ve yüksek iş letimat iletişimi için tasarlanmıştır. gRPC, verimliliğin kritik olduğu hafif mikro hizmetler için mükemmeldir.
* **Noktadan noktaya gerçek zamanlı iletişim** &ndash; gRPC çift yönlü akış için mükemmel bir desteğe sahiptir. gRPC hizmetleri, oy kullanmadan iletileri gerçek zamanlı olarak itebilir.
* **Polyglot ortamları** &ndash; gRPC aracı, gRPC'yi çok dilli ortamlar için iyi bir seçim haline getirerek tüm popüler geliştirme dillerini destekler.
* **Ağ kısıtlı ortamlar** &ndash; gRPC iletileri, hafif bir ileti biçimi olan Protobuf ile seri hale getirilmiştir. GRPC iletisi her zaman eşdeğer bir JSON iletisinden daha küçüktür.

## <a name="grpc-weaknesses"></a>gRPC zayıflıkları

### <a name="limited-browser-support"></a>Sınırlı tarayıcı desteği

Bugün doğrudan bir tarayıcıdan bir gRPC hizmetini aramak mümkün değildir. gRPC ağır HTTP / 2 özellikleri kullanır ve hiçbir tarayıcı bir gRPC istemcisi desteklemek için web istekleri üzerinde gerekli kontrol düzeyini sağlar. Örneğin, tarayıcılar bir arayanın HTTP/2'nin kullanılmasını istemesine veya temel http/2 karelerine erişim sağlamasına izin vermez.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) tarayıcıda sınırlı gRPC desteği sağlayan gRPC ekibinden ek bir teknolojidir. gRPC-Web iki bölümden oluşur: tüm modern tarayıcıları destekleyen bir JavaScript istemcisi ve sunucuda bir gRPC-Web proxy. gRPC-Web istemcisi proxy çağırır ve proxy gRPC istekleri ni gRPC sunucusuna iletecektir.

gRPC'nin tüm özellikleri gRPC-Web tarafından desteklenmez. İstemci ve çift yönlü akış desteklenmez ve sunucu akışı için sınırlı destek vardır.

> [!TIP]
> .NET Core, gRPC-Web için deneysel desteğe sahiptir. Daha <xref:grpc/browser> fazla bilgi için ziyaret edin.

### <a name="not-human-readable"></a>İnsan okunabilir değil

HTTP API istekleri metin olarak gönderilir ve insanlar tarafından okunabilir ve oluşturulabilir.

gRPC iletileri varsayılan olarak Protobuf ile kodlanır. Protobuf göndermek ve almak için verimli olsa da, ikili biçimi insan okunabilir değildir. Protobuf düzgün deserialize için *.proto* dosyasında belirtilen iletinin arabirim açıklamasını gerektirir. Ek takım tel üzerinde Protobuf yükleri analiz etmek ve elle istekleri oluşturmak için gereklidir.

[Sunucu yansıması](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) ve [gRPC komut satırı aracı](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) gibi özellikler ikili Protobuf iletilerine yardımcı olmak için vardır. Ayrıca, Protobuf mesajları [ve JSON dönüşüm](https://developers.google.com/protocol-buffers/docs/proto3#json)desteği. Dahili JSON dönüştürme, hata ayıklama yaparken Protobuf iletilerini insan tarafından okunabilir forma dönüştürmek için etkili bir yol sağlar.

## <a name="alternative-framework-scenarios"></a>Alternatif çerçeve senaryoları

Diğer çerçeveler aşağıdaki senaryolarda gRPC üzerinden önerilir:

* **Tarayıcıda erişilebilir API'ler** &ndash; gRPC tarayıcıda tam olarak desteklenmez. gRPC-Web tarayıcı desteği sunabilir, ancak sınırlamaları vardır ve bir sunucu proxy tanıttı.
* **Yayın gerçek zamanlı iletişim** &ndash; gRPC akış yoluyla gerçek zamanlı iletişimi destekler, ancak kayıtlı bağlantılara bir mesaj yayınlama kavramı yoktur. Örneğin, sohbet odasındaki tüm istemcilere yeni sohbet iletilerinin gönderilmesi gereken bir sohbet odası senaryosunda, istemciye tek tek yeni sohbet iletileri akışı için her gRPC araması gereklidir. [SignalR](xref:signalr/introduction)bu senaryo için yararlı bir çerçevedir. SignalRkalıcı bağlantılar ve iletiyayını için yerleşik destek kavramına sahiptir.
* **İşlemler arası iletişim** &ndash; Bir işlem, gelen gRPC çağrılarını kabul etmek için bir HTTP/2 sunucusu barındırmalıdır. Windows için, süreçler arası iletişim [boruları](/dotnet/standard/io/pipe-operations) hızlı ve hafif bir iletişim yöntemidir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
