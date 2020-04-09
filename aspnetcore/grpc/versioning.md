---
title: gRPC hizmetlerinin sürümünü oluşturma
author: jamesnk
description: gRPC hizmetlerini nasıl sürümedebilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664117"
---
# <a name="versioning-grpc-services"></a>gRPC hizmetlerinin sürümünü oluşturma

Yazar: [James Newton-King](https://twitter.com/jamesnk)

Bir uygulamaya eklenen yeni özellikler, istemcilere sağlanan gRPC hizmetlerinin bazen beklenmedik ve kopan şekillerde değişmesini gerektirebilir. gRPC hizmetleri değiştiğinde:

* Değişikliklerin istemcileri nasıl etkilediğine dikkat edilmelidir.
* Değişiklikleri desteklemek için bir sürüm stratejisi uygulanmalıdır.

## <a name="backwards-compatibility"></a>Geriye dönük uyumluluk

gRPC protokolü, zaman içinde değişen hizmetleri desteklemek üzere tasarlanmıştır. Genellikle, gRPC hizmetleri ve yöntemleri eklemeler kırılmaz. Kırılmayan değişiklikler, varolan istemcilerin değişiklik olmadan çalışmaya devam etmesine olanak sağlar. gRPC hizmetlerinin değiştirilmesi veya silmesi değişiklikleri bozuyor. gRPC hizmetlerinde değişiklikler kırıldığında, bu hizmeti kullanan istemcilerin güncelleştirilip yeniden dağıtılması gerekir.

Bir hizmette kırılmayan değişiklikler yapmanın bir takım avantajları vardır:

* Varolan istemciler çalışmaya devam ediyor.
* İstemcileri değişiklikleri kırma ve güncelleştirme konusunda bilgilendirmek le ilgili çalışmayı önler.
* Hizmetin yalnızca bir sürümünün belgelenmesi ve bakımı gerekir.

### <a name="non-breaking-changes"></a>Kırılmayan değişiklikler

Bu değişiklikler, gRPC protokol düzeyinde ve .NET ikili düzeyinde kırılmaz.

* **Yeni bir hizmet ekleme**
* **Hizmete yeni bir yöntem ekleme**
* **İstek iletisine alan ekleme** - İstek iletisine eklenen alanlar ayarlanmadığında sunucudaki [varsayılan değerle](https://developers.google.com/protocol-buffers/docs/proto3#default) birlikte deserialize edilir. Kesintisiz bir değişiklik olmak için, yeni alan eski istemciler tarafından ayarlanmıyorsa hizmetin başarılı olması gerekir.
* **Yanıt iletisine alan ekleme** - Yanıt iletisine eklenen alanlar iletinin [istemcideki bilinmeyen alanlar](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) koleksiyonuna deserialolarak eklenir.
* **Bir enum için bir değer ekleme** - Enums sayısal bir değer olarak serihale edilir. Yeni enum değerleri, enum adı olmadan istemci üzerinde enum değerine deserialize edilir. Son dakika değişikliği olmak için, eski istemcilerin yeni enum değerini alırken doğru çalışması gerekir.

### <a name="binary-breaking-changes"></a>İkili kırılma değişiklikleri

Aşağıdaki değişiklikler bir gRPC protokolü düzeyinde kırılmaz, ancak istemci nin en son *.proto* sözleşmesine veya istemci .NET derlemesine yükseltilmesi durumunda güncelleştirilmelidir. NuGet'e bir gRPC kitaplığı yayımlamayı planlıyorsanız ikili uyumluluk önemlidir.

* **Alan kaldırma** - Kaldırılan alandaki değerler iletinin [bilinmeyen alanlarına](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)deserialize edilir. Bu bir gRPC protokolü değiştirme kırma değil, ancak istemci en son sözleşmeye yükseltmeleri gerekir güncelleştirilmelidir. Kaldırılan alan numarasının gelecekte yanlışlıkla yeniden kullanılmamış olması önemlidir. Bunun gerçekleşmediğinden emin olmak için, Protobuf'un [ayrılmış](https://developers.google.com/protocol-buffers/docs/proto3#reserved) anahtar sözcük lerini kullanarak iletideki silinmiş alan numaralarını ve adlarını belirtin.
* **İletiyi yeniden adlandırma** - İleti adları genellikle ağda gönderilmez, bu nedenle bu bir gRPC protokolü bozma değişikliği değildir. En son sözleşmeye yükseltilmesi durumunda istemcinin güncellenmesi gerekir. İleti **adlarının** ağda gönderildiği durumlardan biri, ileti adı ileti türünü tanımlamak için kullanıldığında [Herhangi bir](https://developers.google.com/protocol-buffers/docs/proto3#any) alandaki durumdur.
* **csharp_namespace değiştirme** `csharp_namespace` - Değiştirme, oluşturulan .NET türlerinin ad alanını değiştirir. Bu bir gRPC protokolü değiştirme kırma değil, ancak istemci en son sözleşmeye yükseltmeleri gerekir güncelleştirilmelidir.

### <a name="protocol-breaking-changes"></a>Protokol kesme değişiklikleri

Aşağıdaki öğeler protokol ve ikili kesme değişiklikleridir:

* **Bir alanı yeniden adlandırma** - Protobuf içeriğinde alan adları yalnızca oluşturulan kodda kullanılır. Alan numarası ağdaki alanları tanımlamak için kullanılır. Bir alanı yeniden adlandırmak, Protobuf için bir protokol bozan bir değişiklik değildir. Ancak, bir sunucu JSON içeriğini kullanıyorsa, bir alanı yeniden adlandırmak bir kesme değişikliğidir.
* **Alan veri türünü değiştirme** - Bir alanın veri türünü uyumsuz bir [türle](https://developers.google.com/protocol-buffers/docs/proto3#updating) değiştirmek, iletiyi deserializing yaparken hatalara neden olur. Yeni veri türü uyumlu olsa bile, istemcinin en son sözleşmeye yükseltilmesi durumunda yeni türü desteklemek için güncelleştirilmiş olması gerekir.
* **Alan numarası değiştirme** - Protobuf yükleri ile alan numarası ağdaki alanları tanımlamak için kullanılır.
* **Bir paketi, hizmeti veya yöntemi yeniden adlandırma** - gRPC URL'yi oluşturmak için paket adını, hizmet adını ve yöntem adını kullanır. İstemci sunucudan *UYGULANMAMIŞ* bir durum alır.
* **Bir hizmeti veya yöntemi kaldırma** - İstemci kaldırılan yöntemi ararken sunucudan *UYGULANMAMIŞ* bir durum alır.

### <a name="behavior-breaking-changes"></a>Davranış kesme değişiklikleri

Kesintisiz değişiklik yaparken, eski istemcilerin yeni hizmet davranışıyla çalışmaya devam edip edemeyeceğini de göz önünde bulundurmanız gerekir. Örneğin, istek iletisine yeni bir alan ekleme:

* Protokolü bozan bir değişiklik değil.
* Yeni alan ayarlanmıyorsa sunucuda bir hata durumu döndürülme, eski istemciler için bir kırılma değişikliği yapar.

Davranış uyumluluğu uygulamaya özgü kodunuz tarafından belirlenir.

## <a name="version-number-services"></a>Sürüm numarası hizmetleri

Hizmetler geriye eski istemciler ile uyumlu kalmak için çaba göstermelidir. Sonunda uygulamanızda yapılan değişiklikler, son dakika değişikliklerini gerektirebilir. Eski istemcileri kırmak ve onları hizmetinizle birlikte güncelleştirilmeye zorlamak iyi bir kullanıcı deneyimi değildir. Kesme değişiklikleri yaparken geriye doğru uyumluluğu korumanın bir yolu, bir hizmetin birden çok sürümü yayımlamaktır.

gRPC, .NET ad alanı gibi çalışan isteğe bağlı bir [paket](https://developers.google.com/protocol-buffers/docs/proto3#packages) belirteçini destekler. Aslında, *.proto* dosyasında ayarlanmazise `package` `option csharp_namespace` oluşturulan .NET türleri için .NET ad alanı olarak kullanılır. Paket, hizmetiniz ve iletileri için bir sürüm numarası belirtmek için kullanılabilir:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Paket adı, bir hizmet adresini tanımlamak için servis adı ile birleştirilir. Hizmet adresi, bir hizmetin birden çok sürümüne yan yana barındırılmasına izin verir:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Sürümlü hizmetin uygulamaları *Startup.cs*kayıtlıdır:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Paket adına bir sürüm numarası da dahil olmak, değişiklikleri kırarak hizmetinizin *v2* sürümünü yayımlama fırsatı sunarken, *v1* sürümünü arayan eski istemcileri desteklemeye devam edin. İstemciler *v2* hizmetini kullanmak üzere güncelleştirildikten sonra, eski sürümü kaldırmayı seçebilirsiniz. Bir hizmetin birden çok sürümü yayımlanmayı planlarken:

* Makulse değişiklikleri bozmaktan kaçının.
* Kesme değişiklikleri yapmadan sürüm numarasını güncelleştirin.
* Kesme değişiklikleri yaptığınızda sürüm numarasını güncelleştirin.

Bir hizmetin birden çok sürümü yayımlanır. Yinelemeyi azaltmak için, iş mantığını hizmet uygulamalarından eski ve yeni uygulamalar tarafından yeniden kullanılabilecek merkezi bir konuma taşımayı düşünün:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Farklı paket adlarıyla oluşturulan hizmetler ve iletiler **farklıdır .NET türleridir.** İş mantığını merkezi bir konuma taşımak, iletileri yaygın türlere eşleme gerektirir.
