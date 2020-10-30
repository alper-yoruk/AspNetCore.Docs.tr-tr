---
title: Son tarihleri ve iptali olan güvenilir gRPC Hizmetleri
author: jamesnk
description: .NET ' te son tarihler ve iptalle güvenilir gRPC Hizmetleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- appsettings.json
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059929"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Son tarihleri ve iptali olan güvenilir gRPC Hizmetleri

, [James bAyKiNg](https://twitter.com/jamesnk)

Son tarihleri ve iptali, gRPC istemcileri tarafından devam eden çağrıları durdurmak için kullanılan özelliklerdir. Bu makalede, son tarihleri ve iptali önemli ve bunların .NET gRPC uygulamalarında nasıl kullanılacağı açıklanmaktadır.

## <a name="deadlines"></a>Tarihi

Son Tarih, bir gRPC istemcisinin, çağrının tamamlanmasını bekleyeceği süreyi belirtmesini sağlar. Son Tarih aşıldığında, çağrı iptal edilir. Bir çağrının ne kadar süreyle çalıştırılabildiğinden bir üst sınır sağladığından son tarih ayarlama önemlidir. Yanlış çalışan hizmetlerin sürekli olarak çalıştırılmasını ve sunucu kaynaklarının tüketilmesi işlemini sonlandırır. Son tarihler, güvenilir uygulamalar oluşturmak için yararlı bir araçtır ve yapılandırılmalıdır.

Son Tarih yapılandırması:

* Bir son tarih, `CallOptions.Deadline` bir çağrı yapıldığında kullanılarak yapılandırılır.
* Varsayılan son tarih değeri yok. Son Tarih belirtilmediği takdirde gRPC çağrılarının süresi sınırlı değildir.
* Son Tarih, son tarihin aşılacağı UTC zamandır. Örneğin, `DateTime.UtcNow.AddSeconds(5)` Şu anda 5 saniyelik bir son tarih.
* Geçmişte veya geçerli bir zaman kullanılırsa, çağrı son tarihi de aşar.
* Son Tarih, gRPC çağrısıyla birlikte gönderilir ve hem istemci hem de hizmet tarafından bağımsız olarak izlenir. Bir tek makinede gRPC çağrısı tamamlanır, ancak yanıtın istemciye döndürdüğü zamana göre son tarih aşılmıştır.

Son Tarih aşılırsa, istemci ve hizmetin farklı bir davranışı vardır:

* İstemci, temel alınan HTTP isteğini hemen iptal eder ve bir `DeadlineExceeded` hata oluşturur. İstemci uygulaması, hatayı yakalamak ve kullanıcıya bir zaman aşımı iletisi göstermek için seçim yapabilir.
* Sunucusunda, yürütülen HTTP isteği iptal edilir ve [Servercallcontext. CancellationToken](xref:System.Threading.CancellationToken) oluşturulur. HTTP isteği iptal edilse de, gRPC çağrısı, yöntem tamamlanana kadar sunucuda çalışmaya devam eder. İptal belirtecinin çağrı ile birlikte iptal edilmesi için zaman uyumsuz yöntemlere geçirilmesi önemlidir. Örneğin, zaman uyumsuz veritabanı sorgularına ve HTTP isteklerine bir iptal belirteci geçirme. İptal belirtecinin geçirilmesi, iptal edilen çağrının sunucuda hızlı bir şekilde tamamlanmasını ve diğer çağrılar için kaynakların serbest geçirilmesini sağlar.

`CallOptions.Deadline`Bir gRPC çağrısının son tarihini ayarlamak için yapılandırın:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

`ServerCallContext.CancellationToken`GRPC hizmetinde kullanma:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Son tarihleri yayma

Çalıştırılan bir gRPC hizmetinden bir gRPC çağrısı yapıldığında, son tarih yayılmalıdır. Örneğin:

1. `FrontendService.GetUser`Son tarihi olan istemci uygulaması çağrıları.
2. `FrontendService` çağırır `UserService.GetUser` . İstemci tarafından belirtilen son tarih, yeni gRPC çağrısıyla belirtilmelidir.
3. `UserService.GetUser` Son tarihi alır. İstemci uygulamasının son tarihi aşılırsa, doğru zaman aşımına uğrar.

Çağrı bağlamı son tarihi şu şekilde sağlar `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

Son tarihleri el ile yayma, mabera olabilir. Son tarihin her çağrıya geçirilmesi gerekir ve yanlışlıkla kaçırılması kolaydır. GRPC istemci fabrikası ile bir otomatik çözüm bulunur. Belirtme `EnableCallContextPropagation` :

* Son Tarih ve iptal belirtecini alt çağrılara otomatik olarak yayar.
* , Karmaşık, iç içe geçmiş gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Daha fazla bilgi için bkz. <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>İptal Etme

İptal etme, gRPC istemcisinin artık gerekli olmayan uzun çalışan çağrıları iptal etmesine olanak tanır. Örneğin, Kullanıcı bir Web sitesindeki sayfayı ziyaret ettiğinde gerçek zamanlı güncelleştirmeleri akıp bir gRPC çağrısı başlatılır. Kullanıcı sayfadan uzaklaştığında akış iptal edilmelidir.

Bir gRPC çağrısı, [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) ile bir iptal belirteci geçirerek veya çağrıya çağrı yaparak istemcide iptal edilebilir `Dispose` .

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

iptal edilecek gRPC Hizmetleri:
* `ServerCallContext.CancellationToken`Zaman uyumsuz yöntemlere geçirin. Zaman uyumsuz yöntemlerin iptal edilmesi, sunucudaki çağrının hızlı bir şekilde tamamlanmasını sağlar.
* İptal belirtecini alt çağrılara yay. İptal belirtecini yayan alt çağrıların üst öğesiyle iptal edilmesini sağlar. [GRPC istemci fabrikası](xref:grpc/clientfactory) ve `EnableCallContextPropagation()` iptal belirtecini otomatik olarak yayar.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/client>
* <xref:grpc/clientfactory>
