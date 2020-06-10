---
title: gRPC hizmetlerini HTTP API’leriyle karşılaştırma
author: jamesnk
description: GRPC 'nin HTTP API 'Leri ile nasıl Karşılaştırıldığı ve bu senaryonun önerdiğimiz ne olduğunu öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/comparison
ms.openlocfilehash: f622a1518781c255d36762dc651f975625dabf7c
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106136"
---
# <a name="compare-grpc-services-with-http-apis"></a>gRPC hizmetlerini HTTP API’leriyle karşılaştırma

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu makalede, [GRPC HIZMETLERININ](https://grpc.io/docs/guides/) JSON Ile HTTP API 'lerle (ASP.NET Core [Web API 'leri](xref:web-api/index)dahil) nasıl karşılaştırılacağı açıklanmaktadır. Uygulamanız için bir API sağlamak için kullanılan teknoloji önemli bir seçimdir ve gRPC, HTTP API 'Lerine kıyasla benzersiz avantajlar sunmaktadır. Bu makalede, gRPC 'nin güçlü ve zayıf yönleri ele alınmaktadır ve gRPC 'nin diğer teknolojiler üzerinden kullanılması için senaryolar önerilmektedir.

## <a name="high-level-comparison"></a>Üst düzey karşılaştırma

Aşağıdaki tabloda, gRPC ve HTTP API 'Leri arasında JSON ile yüksek düzeyde bir karşılaştırma sunulmaktadır.

| Öne çıkan özelliği          | gRPC                                               | JSON ile HTTP API 'Leri           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Sözleşme         | Gerekli (*. proto*)                                | İsteğe bağlı (Openapı)            |
| Protokol         | HTTP/2                                             | HTTP                          |
| Te          | [Prototip (küçük, ikili)](#performance)           | JSON (büyük, insan tarafından okunabilir)  |
| Dikkatli olun | [Katı belirtim](#strict-specification)      | Miş. Herhangi bir HTTP geçerlidir.     |
| Akış        | [İstemci, sunucu, iki yönlü](#streaming)       | İstemci, sunucu                |
| Tarayıcı desteği  | [Hayır (GRPC-Web gerektirir)](#limited-browser-support) | Yes                           |
| Güvenlik         | Taşıma (TLS)                                    | Taşıma (TLS)               |
| İstemci kod oluşturma | [Evet](#code-generation)                      | Openapı + üçüncü taraf araçları |

## <a name="grpc-strengths"></a>gRPC güçleri

### <a name="performance"></a>Performans

gRPC iletileri, etkin bir ikili ileti biçimi olan [Protoarabelleğe](https://developers.google.com/protocol-buffers/docs/overview)kullanılarak serileştirilir. Sunucu ve istemcide prototip çok hızlı hale getirir. Prototip serileştirme, mobil uygulamalar gibi sınırlı bant genişliği senaryolarında önemli olan küçük ileti yüklerine neden olur.

gRPC, http 1. x üzerinden önemli performans avantajları sağlayan büyük bir HTTP düzeltmesi olan HTTP/2 için tasarlanmıştır:

* İkili çerçeveleme ve sıkıştırma. HTTP/2 Protokolü, hem gönderme hem de alma sırasında kompakt ve verimlidir.
* Tek bir TCP bağlantısı üzerinden birden çok HTTP/2 çağrısının çoğullama. Çoğullama [, satır başı engellemeyi](https://en.wikipedia.org/wiki/Head-of-line_blocking)ortadan kaldırır.

HTTP/2, gRPC 'ye özel değildir. JSON ile HTTP API 'Leri de dahil olmak üzere birçok istek türü, HTTP/2 kullanabilir ve performans geliştirmelerinden faydalanabilir.

### <a name="code-generation"></a>Kod oluşturma

Tüm gRPC çerçeveleri, kod oluşturma için birinci sınıf destek sağlar. GRPC geliştirmeye yönelik bir çekirdek dosya, gRPC Hizmetleri ve iletilerinin sözleşmesini tanımlayan [. proto dosyasıdır](https://developers.google.com/protocol-buffers/docs/proto3). Bu dosya gRPC çerçevelerinden kod, bir hizmet temel sınıfı, iletiler ve tüm istemci oluşturur.

Sunucu ile istemci arasında *. proto* dosyasını paylaşarak iletiler ve istemci kodu uçtan uca oluşturulabilir. İstemcinin kod üretimi, istemci ve sunucudaki iletilerin çoğaltılmasını ortadan kaldırır ve sizin için kesin olarak belirlenmiş bir istemci oluşturur. İstemci yazmak gerekmez, birçok hizmet içeren uygulamalarda önemli geliştirme süresini kaydeder.

### <a name="strict-specification"></a>Katı belirtim

JSON ile HTTP API 'SI için biçimsel belirtim yok. Geliştiriciler, URL 'Lerin, HTTP fiillerinin ve yanıt kodlarının en iyi biçimini kaldırır.

[GRPC belirtimi](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) , bir GRPC hizmetinin izlenmesi gereken biçim hakkında ayrıntılı bir şekilde yapılır. GRPC, platformlar ve uygulamalar arasında tutarlı olduğundan, GRPC başarılacağı 'yı ortadan kaldırır ve geliştirici süresini kaydeder.

### <a name="streaming"></a>Akış

HTTP/2, uzun süreli, gerçek zamanlı iletişim akışları için bir temel sağlar. gRPC, HTTP/2 üzerinden akış için birinci sınıf destek sağlar.

GRPC hizmeti tüm akış kombinasyonlarını destekler:

* Birli (akış yok)
* Sunucudan istemciye akışa
* İstemciden sunucuya akışa
* İki yönlü akış

### <a name="deadlinetimeouts-and-cancellation"></a>Son tarih/zaman aşımları ve iptal

gRPC, istemcilerin bir RPC 'nin tamamlanmasını beklemek zorunda kalabilecekleri süreyi belirtmesini sağlar. [Son tarih](https://grpc.io/blog/deadlines) sunucuya gönderilir ve sunucu son tarihi aşarsa hangi eylemin yapılacağını seçebilir. Örneğin, sunucu devam eden gRPC/HTTP/veritabanı isteklerini zaman aşımında iptal edebilir.

Son tarihi ve iptali alt gRPC çağrıları aracılığıyla yayın, kaynak kullanım sınırlarının uygulanmasını sağlar.

## <a name="grpc-recommended-scenarios"></a>gRPC önerilen senaryolar

gRPC aşağıdaki senaryolara uygundur:

* **Mikro hizmetler**: GRPC, düşük gecikme süresi ve yüksek işleme iletişimi için tasarlanmıştır. gRPC, verimlilik açısından kritik olan hafif mikro hizmetler için harika.
* **Noktadan noktaya gerçek zamanlı iletişim**: GRPC, iki yönlü akış için harika desteğe sahiptir. gRPC Hizmetleri, yoklama yapmadan iletileri gerçek zamanlı olarak gönderebilir.
* **Çok yönlü ortamları**: GRPC araçları, tüm popüler geliştirme dillerini destekler ve bu da GRPC 'yi çok dilli ortamlar için iyi bir seçenek yapar.
* **Ağ kısıtlamalı ortamlar**: GRPC iletileri, hafif bir ileti biçimi olan protoarabellek ile serileştirilir. GRPC iletisi her zaman denk bir JSON iletisinden daha küçüktür.

## <a name="grpc-weaknesses"></a>gRPC zayıflığı

### <a name="limited-browser-support"></a>Sınırlı tarayıcı desteği

Doğrudan bir tarayıcıda bir gRPC hizmetini doğrudan çağırmak olanaksızdır. gRPC, HTTP/2 özelliklerini çok fazla kullanır ve tarayıcı, bir gRPC istemcisini desteklemek için Web istekleri üzerinde gerekli denetim düzeyini sağlar. Örneğin, tarayıcılar arayan HTTP/2 ' nin kullanılmasına izin vermez veya temel alınan HTTP/2 çerçevelerine erişim sağlar.

[GRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) , GRPC ekibinin tarayıcıda sınırlı GRPC desteği sağlayan ek bir teknolojidir. gRPC-Web iki bölümden oluşur: tüm modern tarayıcıları ve sunucudaki gRPC-Web proxy 'sini destekleyen bir JavaScript istemcisi. GRPC-Web istemcisi ara sunucuyu çağırır ve proxy, gRPC isteklerini gRPC sunucusuna iletir.

GRPC 'nin özelliklerinin hepsi gRPC-Web tarafından desteklenmez. İstemci ve iki yönlü akış desteklenmez ve sunucu akışı için sınırlı destek vardır.

> [!TIP]
> .NET Core 'da gRPC-Web için deneysel destek vardır. <xref:grpc/browser>Daha fazla bilgi için ziyaret edin.

### <a name="not-human-readable"></a>Okunabilir değil

HTTP API istekleri metin olarak gönderilir ve insanların okuyabilmesi ve oluşturulabilmesi olabilir.

gRPC iletileri varsayılan olarak Protodeğer ile kodlanır. Protoarabellek gönderme ve alma açısından verimli olsa da, ikili biçimi insanlar tarafından okunabilir değildir. Protoarabelleğe doğru bir şekilde seri durumdan çıkarmak için *. proto* dosyasında belirtilen iletinin arabirim açıklaması gereklidir. Ek araçlar, hat üzerindeki prototiplerin yüklerini analiz etmek ve istekleri el ile oluşturmak için gereklidir.

[Sunucu yansıtma](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) ve [GRPC komut satırı aracı](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) gibi özellikler, ikili prototipsiz iletilerle yardım etmek için mevcuttur. Ayrıca, prototipli mesajlar [JSON öğesine ve öğesinden dönüştürmeyi](https://developers.google.com/protocol-buffers/docs/proto3#json)destekler. Yerleşik JSON dönüştürmesi, hata ayıklarken prototip iletileri okunabilir ve okunabilir biçime dönüştürmek için etkili bir yol sağlar.

## <a name="alternative-framework-scenarios"></a>Alternatif Framework senaryoları

Aşağıdaki senaryolarda gRPC üzerinden diğer çerçeveler önerilir:

* **Tarayıcıda erişilebilen API 'ler**: GRPC tarayıcıda tam olarak desteklenmez. gRPC-Web tarayıcı desteği sunabilir, ancak sınırlamaları vardır ve sunucu proxy 'sini tanıtır.
* **Gerçek zamanlı Iletişim yayınlama**: GRPC akış aracılığıyla gerçek zamanlı iletişimi destekler, ancak kayıtlı bağlantılara bir ileti yayınlama kavramı mevcut değildir. Örneğin, sohbet odasındaki tüm istemcilere yeni sohbet iletilerinin gönderilmesi gereken bir sohbet odası senaryosunda her bir gRPC çağrısı, istemciye yeni sohbet iletilerini tek tek akışa almak için gereklidir. [SignalR](xref:signalr/introduction)Bu senaryo için kullanışlı bir çerçevedir. SignalR, sürekli bağlantılar ve yayın iletileri için yerleşik destek kavramıdır.
* İşlemler **arası iletişim**: bir işlemin, gelen GRPC çağrılarını kabul etmek IÇIN bir http/2 sunucusunu barındırması gerekir. Windows için, işlemler arası iletişim [kanalları](/dotnet/standard/io/pipe-operations) hızlı ve hafif bir iletişim yöntemidir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
