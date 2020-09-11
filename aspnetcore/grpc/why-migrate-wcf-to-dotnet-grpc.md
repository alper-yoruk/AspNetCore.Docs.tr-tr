---
title: WCF 'yi ASP.NET Core gRPC 'ye neden geçirin
author: markrendle
description: Bu makalede, ASP.NET Core gRPC 'nin neden modern mimarilere ve platformlara geçiş yapmak isteyen Windows Communication Foundation (WCF) geliştiricileri için uygun bir Özet olduğunu bulabilirsiniz.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 811e6037b058b26fcf91063123d04d448a9a28a8
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90012762"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>Windows Communication Foundation için gRPC (WCF) geliştiricileri

Bu makalede, ASP.NET Core gRPC 'nin neden modern mimarilere ve platformlara geçiş yapmak isteyen Windows Communication Foundation (WCF) geliştiricileri için uygun bir Özet olduğunu bulabilirsiniz.

## <a name="comparison-to-wcf"></a>WCF ile karşılaştırma

Uygulama ve yaklaşım gRPC için farklı olsa da, gRPC ile hizmetleri geliştirme ve kullanma deneyimi WCF geliştiricileri için sezgisel olmalıdır. WCF ve gRPC, aynı hedeflere sahip RPC (uzak yordam çağrısı) çerçevelerdir:

* İstemci ve sunucu aynı platformda olsa da kodun kodda olmasını mümkün hale getirin.
* Basitleştirilmiş bir taşınabilir ağ API 'SI sağlayın.

Her iki platform da arabirim bildirme ve uygulama gereksinimini paylaşır, ancak arabirimi bildirme işlemi farklıdır. GRPC 'nin, WCF Hizmetleri için kullanılabilen bağlamalara iyi eşleme desteklediği birçok RPC çağrısı türü. Daha fazla bilgi ve örnek için bkz. [WCF çözümünü gRPC 'ye geçirme](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>GRPC 'nin avantajları

gRPC aşağıdaki nedenlerden dolayı diğer yaklaşımlardan daha iyi bir çerçeve sağlar.

### <a name="performance"></a>Performans

gRPC, HTTP/2 kullanır. HTTP/1.1 'nin aksine, HTTP/2:

* Daha küçük, daha hızlı bir ikili protokoldür.
* , Bilgisayarların ayrıştırılmasında daha etkilidir.
* Tek bir bağlantı üzerinden çoğullama isteklerini destekler. Çoğullama, birden çok isteğin bir bağlantı üzerinden, birbirini engelleyen istekler olmadan gönderilmesini sağlar. HTTP/1.1 ' de, engelleme "satır başı (HOL) engelleme" olarak bilinir.

gRPC, iletileri seri hale getirmek için etkin bir ikili biçimi olan Protoarabelleği kullanır. Prototipsiz iletiler şunlardır:
* Serileştirme ve seri durumdan çıkarma hızlı.
* Metin tabanlı biçimlerden daha az bant genişliği kullanın. 

gRPC, bant genişliği kısıtlamalarına sahip mobil cihazlar ve ağlar için iyi bir çözümdür.

### <a name="interoperability"></a>Birlikte Çalışabilirlik

.NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby ve PHP dahil olmak üzere tüm önemli programlama dilleri ve platformları için gRPC araçları ve kitaplıkları vardır. Her platform için Prototipsiz ikili kablo biçimi ve verimli kod üretimi sayesinde geliştiriciler platformlar arası, performanslı uygulamalar oluşturabilir.

### <a name="usability-and-productivity"></a>Kullanılabilirlik ve üretkenlik

gRPC, kapsamlı bir RPC çözümüdür. Birden çok dil ve platformda sürekli olarak işe yarar. Ayrıca, ortak kodların büyük bölümü otomatik olarak oluşturulan mükemmel araçlar da sağlar. WCF gibi gRPC, iletileri ve kesin olarak belirlenmiş bir istemciyi otomatik olarak oluşturur. Geliştirici saati, iş mantığına odaklanmak için serbest bırakılır.

### <a name="streaming"></a>Akış

gRPC 'de, WCF 'nin tam çift yönlü hizmetlerine benzer işlevler sağlayan tam çift yönlü akış vardır. gRPC akışı, normal internet bağlantıları, yük dengeleyiciler ve hizmet kafesleri üzerinden çalışabilir.

### <a name="deadlines-timeouts-and-cancellation"></a>Son tarihler, zaman aşımları ve iptal

gRPC, istemcilerin bir RPC 'nin tamamlaması için en uzun süreyi belirtmesini sağlar. Belirtilen son tarih aşılırsa sunucu, işlemi istemciden bağımsız olarak iptal edebilir. Son tarihler ve iptaller, kaynak kullanım sınırlarını zorlamaya yardımcı olmak için sonraki gRPC çağrıları aracılığıyla yayılamaz. İstemciler, son tarih aşıldığında veya gerekirse daha önceki işlemleri durdurabilir. Örneğin, istemciler Kullanıcı etkileşimi nedeniyle işlemleri durdurabilir.

### <a name="security"></a>Güvenlik

gRPC, istemci ve sunucu arasında uçtan uca şifreli bir bağlantı sağlamak için TLS ve HTTP/2 kullanabilir. İstemci sertifikası kimlik doğrulaması desteği, istemci ve sunucu arasındaki güvenliği ve güveni daha da artırır.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>WCF için .NET Core ve .NET 5 ' e geçiş yolu olarak gRPC

.NET Core ve .NET 5, Microsoft 'un bir dizi platformda hizmet sunmak isteyen geliştiricilere uzaktan iletişim çözümleri sunma biçiminde bir kaydırma işareti işaretler. .NET Core ve .NET 5, [WCF hizmetlerini çağırmayı](/dotnet/core/additional-tools/wcf-web-service-reference-guide)destekler, ancak WCF barındırma için sunucu tarafı desteği sunmaz.

WCF uygulamalarını modernize etmek için önerilen iki yol vardır:

* gRPC, modern teknolojiler üzerine kurulmuştur ve RPC uygulamalarına yönelik geliştirici topluluğu genelinde en popüler seçim olarak ortaya çıktı. .NET Core 3,0 ile başlayarak, modern .NET platformları gRPC için harika desteğe sahiptir. WCF hizmetlerinin gRPC kullanacak şekilde geçirilmesi, Modern uygulamalarda gerekli olan RPC özelliklerinin, performansının sağlanmasına yardımcı olur.

* [Corewcf](https://github.com/CoreWCF/CoreWCF) , WCF hizmetlerinin .NET Core ve .NET 5 ' e barındırılmasına yönelik destek almak için bir topluluk çabasında bulunur. Bir önizleme sürümü kullanılabilir ve proje, üretime hazır olma aşamasında çalışıyor. CoreWCF yalnızca WCF özelliklerinin bir alt kümesini destekler ve bunu kullanmak için geçiş yapan .NET Framework uygulamalar, kod değişiklikleri ve test işleminin başarılı olması için gerekli olacaktır. Bir uygulama, WCF Hizmetleri çağıran mevcut istemcilerle uyumluluğu sürdürmek için gerekliyse, CoreWCF iyi bir seçenektir.

## <a name="get-started"></a>başlarken

WCF geliştiricileri için ASP.NET Core gRPC hizmetleri oluşturma hakkında ayrıntılı yönergeler için bkz. [WCF geliştiricileri için ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)
