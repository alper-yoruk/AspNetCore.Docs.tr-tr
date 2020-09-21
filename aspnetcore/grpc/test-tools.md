---
title: ASP.NET Core 'de Grpkıvle gRPC hizmetlerini test edin
author: jamesnk
description: GRPC araçlarıyla Hizmetleri test etme hakkında bilgi edinin. gRPC hizmetleriyle etkileşim kurmak için bir komut satırı aracını GRP. Grpcuı etkileşimli bir Web Kullanıcı arabirimi.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770174"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>ASP.NET Core 'de Grpkıvle gRPC hizmetlerini test edin

, [James bAyKiNg](https://twitter.com/jamesnk)

Araç, geliştiricilerin istemci uygulamaları oluşturmadan Hizmetleri test etmesine olanak tanıyan gRPC için kullanılabilir:

* [Grpkıvrık](https://github.com/fullstorydev/grpcurl) , GRPC hizmetleriyle etkileşim sağlayan bir komut satırı aracıdır.
* [Grpcuı](https://github.com/fullstorydev/grpcui) , GRP-on üst kısmında derleme yapar ve GRPC Için Postman ve Swagger Kullanıcı arabirimi gibi araçlara benzer bir etkileşimli Web Kullanıcı arabirimi ekler.

Bu makalede nasıl yapılacağı açıklanır:

* Grpkıvrık ve Grpcuı indirip yükleyin.
* GRPC yansımasını bir gRPC ASP.NET Core uygulamasıyla ayarlayın.
* İle gRPC hizmetlerini bulur ve test edin `grpcurl` .
* Kullanarak bir tarayıcı aracılığıyla gRPC hizmetleriyle etkileşim kurun `grpcui` .

## <a name="about-grpcurl"></a>Grpkıvrımlı hakkında

Grpkıvrık, gRPC topluluğu tarafından oluşturulan bir komut satırı aracıdır. Özellikleri şunları içerir:

* Akış Hizmetleri dahil gRPC hizmetlerini çağırma.
* [GRPC yansımasını](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)kullanan hizmet bulma.
* GRPC hizmetlerini listeleme ve açıklama.
* Güvenli (TLS) ve güvensiz (düz metin) sunucularla birlikte geçerlidir.

İndirme ve yükleme hakkında daha fazla bilgi için `grpcurl` bkz. [Grpkıvrımlı GitHub giriş sayfası](https://github.com/fullstorydev/grpcurl#installation).

![Grpkıvrımlı komut satırı](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>GRPC yansımasını ayarlama

`grpcurl` Hizmetleri çağırabilmeniz için önce hizmet sözleşmesi 'nin bir anlaşması bilmelidir. Bunu yapmak için iki yol vardır:

* Sunucuda [GRPC yansımasını](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) ayarlayın. Grpkıvrımlı, hizmet sözleşmelerini otomatik olarak bulur.
* `.proto`Grphizalamak için komut satırı bağımsız değişkenlerinde dosyaları belirtin.

GRPC yansımasıyla Grpkıvtı kullanmak daha kolay. gRPC Reflection, uygulamanın Hizmetleri bulma için çağırabildiği uygulamaya yeni bir gRPC hizmeti ekler.

gRPC ASP.NET Core, paket ile gRPC yansıtma için yerleşik desteğe sahiptir [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) . Bir uygulamadaki yansımayı yapılandırmak için:

* Bir `Grpc.AspNetCore.Server.Reflection` paket başvurusu ekleyin.
* Yansımayı Kaydet `Startup.cs` :
  * `AddGrpcReflection` yansımayı etkinleştiren Hizmetleri kaydetmek için.
  * `MapGrpcReflectionService` bir yansıma hizmeti uç noktası eklemek için.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

GRPC yansıması ayarlandığında:

* Sunucu uygulamasına bir gRPC yansıma hizmeti eklenir.
* GRPC yansımasını destekleyen istemci uygulamaları, sunucu tarafından barındırılan Hizmetleri bulmaya yönelik yansıma hizmetini çağırabilir.
* gRPC hizmetleri yine de istemciden çağırılır. Yansıma yalnızca hizmet bulmayı ve sunucu tarafı güvenliği atlamalarını mümkün değildir. [Kimlik doğrulama ve yetkilendirme](xref:grpc/authn-and-authz) tarafından korunan uç noktalar, çağıranın, uç nokta için kimlik bilgilerini başarıyla çağrılacak şekilde geçmesini gerektirir.

## <a name="use-grpcurl"></a>`grpcurl` komutunu kullanma

`-help`Bağımsız değişkeni `grpcurl` komut satırı seçeneklerini açıklar:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Hizmetleri bulma

`describe`Sunucu tarafından tanımlanan hizmetleri görüntülemek için fiili ' i kullanın:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Önceki örnek:

* `describe`Sunucuda fiili çalıştırır `localhost:5001` .
* GRPC yansımasıyla döndürülen Hizmetleri ve yöntemleri yazdırır.
  * `Greeter` uygulama tarafından uygulanan bir hizmettir.
  * `ServerReflection` hizmet paket tarafından eklendi `Grpc.AspNetCore.Server.Reflection` .

`describe`Ayrıntılarını görüntülemek için bir hizmet, yöntem veya ileti adıyla birleştirin:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>GRPC hizmetlerini çağırma

İstek iletisini temsil eden bir JSON bağımsız değişkeniyle birlikte bir hizmet ve Yöntem adı belirterek bir gRPC hizmeti çağırın. JSON, Protoarabelleğe dönüştürülüp hizmete gönderilir.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Yukarıdaki örnekte:

* `-d`Bağımsız DEĞIŞKENI JSON ile bir istek iletisi belirtir. Bu bağımsız değişken, sunucu adresi ve yöntem adından önce gelmelidir.
* `SayHello`Hizmeti üzerinde yöntemini çağırır `greeter.Greeter` .
* Yanıt iletisini JSON olarak yazdırır.

## <a name="about-grpcui"></a>Grpcuı hakkında

Grpcuı, gRPC için etkileşimli bir Web Kullanıcı arabirimi. Grpwrapper 'ın üzerine oluþturulur ve gRPC hizmetlerini keşfetmek ve test etmek için Postman veya Swagger Kullanıcı arabirimi gibi HTTP araçlarına benzer bir GUI sağlar.

İndirme ve yükleme hakkında bilgi için `grpcui` bkz. [Grpcuı GitHub ana sayfası](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>`grpcui` kullanma

`grpcui`Bir bağımsız değişken olarak etkileşimde bulunmak için sunucu adresiyle çalıştırın:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Araç etkileşimli Web Kullanıcı arabirimi ile bir tarayıcı penceresi başlatır. gRPC Hizmetleri, gRPC yansıması kullanılarak otomatik olarak bulunur.

![Grpcuı Web Kullanıcı arabirimi](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [Grpkıvrımlı GitHub giriş sayfası](https://github.com/fullstorydev/grpcurl)
* [Grpcuı GitHub giriş sayfası](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
