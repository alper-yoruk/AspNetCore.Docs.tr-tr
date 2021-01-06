---
title: gRPC hizmetlerinin sürümünü oluşturma
author: jamesnk
description: GRPC hizmetlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058707"
---
# <a name="versioning-grpc-services"></a>gRPC hizmetlerinin sürümünü oluşturma

, [James bAyKiNg](https://twitter.com/jamesnk)

Bir uygulamaya eklenen yeni özellikler, bazı durumlarda bazen beklenmedik ve kırılmaya karşı istemciler için sunulan gRPC hizmetlerini gerektirebilir. GRPC Hizmetleri değiştiğinde:

* Değişikliklerin istemcileri nasıl etkilediğini göz önünde bulundurmanız gerekir.
* Değişiklikleri desteklemek için sürüm oluşturma stratejisi uygulanmalıdır.

## <a name="backwards-compatibility"></a>Geriye dönük uyumluluk

GRPC protokolü, zaman içinde değişen Hizmetleri destekleyecek şekilde tasarlanmıştır. Genel olarak, gRPC Hizmetleri ve yöntemlerine yapılan ekler kırılmamış değildir. Bölünmez değişiklikler mevcut istemcilerin değişiklik yapmadan çalışmaya devam etmesine izin verir. GRPC hizmetlerini değiştirme veya silme, değişiklikler ortadan kaldırılır. GRPC hizmetlerinde son değişiklikler olduğunda, bu hizmeti kullanan istemcilerin güncellenmesi ve yeniden dağıtılması gerekir.

Bir hizmette önemli olmayan değişiklikler yapmak çok sayıda avantaj sunar:

* Mevcut istemciler çalışmaya devam eder.
* , Bir veya daha fazla değişiklik için istemcileri bildirmeye ve bunları güncelleştirmeye dahil olan çalışmayı önler.
* Hizmetin yalnızca bir sürümünün belgelenilmesi ve saklanması gerekir.

### <a name="non-breaking-changes"></a>Kırılamayan değişiklikler

Bu değişiklikler, gRPC protokol düzeyinde ve .NET ikili düzeyinde kırılmamış değildir.

* **Yeni bir hizmet ekleniyor**
* **Bir hizmete yeni bir yöntem ekleme**
* **İstek iletisine alan ekleme** -bir istek iletisine eklenen alanlar, ayarlanmayan sunucu üzerindeki [varsayılan değerle](https://developers.google.com/protocol-buffers/docs/proto3#default) seri durumdan silinir. Yeni alan eski istemciler tarafından ayarlanmamışsa, bir olmayan değişiklik olması için hizmetin başarılı olması gerekir.
* **Yanıt iletisine bir alan ekleme** -yanıt iletisine eklenen alanlar, istemcideki [Bilinmeyen alanlar](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) koleksiyonuna seri durumdan çıkarılacak.
* **Sabit listesine değer eklemek** sayısal bir değer olarak serileştirilir. Yeni Enum değerleri, bir sabit listesi adı olmadan, istemcide seri hale getirilmesi değeri olarak seri durumdan silinir. Yeni bir sabit listesi değeri alınırken, daha eski istemcilerin düzgün çalışması gerekir.

### <a name="binary-breaking-changes"></a>İkili son değişiklikler

Aşağıdaki değişiklikler bir gRPC protokol düzeyinde kırılmamış, ancak en son *. proto* sözleşmesine veya istemci .net derlemesine yükseltiyorsa istemcinin güncelleştirilmesi gerekir. Bir gRPC kitaplığını NuGet 'e yayımlamayı planlıyorsanız ikili uyumluluk önemlidir.

* Kaldırılan bir alandan **alan değerlerinin kaldırılması** , iletinin [Bilinmeyen alanlarına](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)seri durumdan çıkarılır. Bu bir gRPC protokol bölünmesi değişikliği değildir, ancak en son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmesi gerekir. Kaldırılmış bir alan numarasının gelecekte yanlışlıkla yeniden kullanılması önemlidir. Bunun gerçekleşmediğinden emin olmak için, Protoarabelleğe ait [ayrılmış](https://developers.google.com/protocol-buffers/docs/proto3#reserved) anahtar sözcüğünü kullanarak, silinen alan numaralarını ve iletideki adları belirtin.
* **Iletiyi yeniden adlandırma** -ileti adları genellikle ağ üzerinde gönderilmemektedir, bu nedenle bu bir GRPC protokol bölünmesi değişikliği değildir. En son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmeleri gerekir. İleti **adlarının ağ** üzerinde gönderildiği bir durum, ileti adının ileti türünü tanımlamak için [kullanıldığı bir durumdur](https://developers.google.com/protocol-buffers/docs/proto3#any) .
* **Csharp_namespace** değiştirme değiştirmek `csharp_namespace` , oluşturulan .net türlerinin ad alanını değiştirecek. Bu bir gRPC protokol bölünmesi değişikliği değildir, ancak en son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmesi gerekir.

### <a name="protocol-breaking-changes"></a>Protokol bölünmesi değişiklikleri

Aşağıdaki öğeler protokol ve ikili parçalara ayırma değişiklerdir:

* **Alanı yeniden adlandırma** -prototip içeriğiyle, alan adları yalnızca oluşturulan kodda kullanılır. Alan numarası, ağdaki alanları tanımlamak için kullanılır. Bir alanı yeniden adlandırmak, Protoarabelleğe yönelik protokol bölünmesi değişikliği değildir. Ancak, bir sunucu JSON içeriğini kullanıyorsa, bir alanı yeniden adlandırmak, Son değişiklik olur.
* **Alan veri türünü değiştirme** -bir alanın veri türünü [uyumsuz bir türe](https://developers.google.com/protocol-buffers/docs/proto3#updating) değiştirme, iletinin serisi kaldırılırken hatalara neden olur. Yeni veri türü uyumlu olsa bile, en son sözleşmeye yükseltiyorsa istemcinin yeni türü destekleyecek şekilde güncelleştirilmesi gerekir.
* **Alan numarasını değiştirme** -prototip yükleri ile, alan numarası ağdaki alanları tanımlamak için kullanılır.
* **Bir paketi, hizmet veya yöntemi yeniden adlandırma** -GRPC, URL 'yi oluşturmak için paket adını, hizmet adını ve yöntem adını kullanır. İstemci sunucudan *uygulanmayan* bir durum alır.
* **Bir hizmet veya yöntemi kaldırma** -istemci, kaldırılan yöntemi çağırırken sunucudan *uygulanmayan* bir durum alır.

### <a name="behavior-breaking-changes"></a>Davranış son değişiklikleri

Kırılmamış olmayan değişiklikler yaparken, eski istemcilerin yeni hizmet davranışıyla çalışmaya devam edip etmediğini de göz önünde bulundurmanız gerekir. Örneğin, bir istek iletisine yeni bir alan ekleme:

* Bir protokol bölünmesi değişikliği değildir.
* Yeni alan ayarlanmamışsa sunucuya bir hata durumu döndürülüyor, eski istemciler için bir değişiklik yapmaz.

Davranış uyumluluğu, uygulamaya özgü kodunuz tarafından belirlenir.

## <a name="version-number-services"></a>Sürüm numarası Hizmetleri

Hizmetler, eski istemcilerle geriye dönük olarak uyumlu kalacak şekilde çalışır. Uygulamanızda yapılan değişiklikler, son değişiklikleri gerektirebilir. Eski istemcileri bozun ve hizmetinize birlikte güncelleştirilmesini zorluyor, iyi bir kullanıcı deneyimi değildir. Geriye dönük uyumluluğu sağlamanın bir yolu, büyük değişiklikler yapılırken bir hizmetin birden çok sürümünü yayımlamaktır.

gRPC, .NET ad alanı gibi işlev gören isteğe bağlı bir [paket](https://developers.google.com/protocol-buffers/docs/proto3#packages) tanımlayıcısını destekler. Aslında, `package` `option csharp_namespace` *. proto* dosyasında ayarlanmamışsa, oluşturulan .net türleri için .net ad alanı olarak kullanılır. Paket, hizmetiniz ve iletileri için bir sürüm numarası belirtmek için kullanılabilir:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Hizmet adresini tanımlamak için paket adı hizmet adıyla birleştirilir. Hizmet adresi, bir hizmetin birden çok sürümünün yan yana barındırılmasına izin verir:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Sürümlenmiş hizmetin uygulamaları *Startup.cs*'ye kaydedilir:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Paket adında bir sürüm numarası dahil olmak üzere, bir *v2* sürümünü, son değişiklikler ile yayımlama olanağı sağlar. bu sayede *v1* sürümünü çağıran eski istemcileri desteklemeye devam edebilirsiniz. İstemciler, *v2* hizmetini kullanmak üzere güncelleştirildikten sonra eski sürümü kaldırmayı seçebilirsiniz. Bir hizmetin birden çok sürümünü yayımlamayı planlarken:

* Makul olursa değişikliklerden kaçının.
* Son değişiklik yapmadığınız takdirde sürüm numarasını güncelleştirmeyin.
* Büyük değişiklikler yaptığınızda sürüm numarasını güncelleştirin.

Bir hizmetin birden çok sürümünün yayımlanması onu çoğaltır. Yinelemeyi azaltmak için, iş mantığını hizmet uygulamalarından eski ve yeni uygulamalar tarafından yeniden kullanılabilen merkezi bir konuma taşımayı göz önünde bulundurun:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Farklı paket adlarıyla oluşturulan hizmetler ve mesajlar **farklı .net türlerdir**. İş mantığını merkezi bir konuma taşımak, iletilerin ortak türlere eşlenmelerini gerektirir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/protobuf>
