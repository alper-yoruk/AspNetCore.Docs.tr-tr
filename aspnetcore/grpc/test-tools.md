---
title: GRPC araçlarıyla test hizmetleri
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594433"
---
# <a name="test-services-with-grpc-tools"></a>GRPC araçlarıyla test hizmetleri

, [James bAyKiNg](https://twitter.com/jamesnk)

Araç, geliştiricilerin istemci uygulamaları oluşturmadan Hizmetleri test etmesine olanak tanıyan gRPC için kullanılabilir. [Grpkıvrık](https://github.com/fullstorydev/grpcurl) , GRPC hizmetleriyle etkileşim sağlayan bir komut satırı aracıdır. [Grpcuı](https://github.com/fullstorydev/grpcui) , GRPC için etkileşimli bir Web Kullanıcı arabirimi ekler.

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

## <a name="setup-grpc-reflection"></a>GRPC yansımasını ayarlama

`grpcurl` , Hizmetleri çağırabilmesi için bunların Prototipsiz sözleşme anlaşması gerektiğini bilmelidir. Bunu yapmak için iki yol vardır:

* Hizmet sözleşmelerini saptamak için gRPC yansımasını kullanın.
* Komut satırı bağımsız değişkenlerinde *. proto* dosyalarını belirtin.

GRPC yansıtma ve hizmet bulma ile Grpkıvtı kullanmak daha kolay. gRPC ASP.NET Core GRPC [. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) paketiyle GRPC yansıması için yerleşik desteğe sahiptir. Bir uygulamadaki yansımayı yapılandırmak için:

* `Grpc.AspNetCore.Server.Reflection`Paket başvurusu Ekle.
* Yansımayı *Startup.cs*'e Kaydet:
  * `AddGrpcReflection` yansımayı etkinleştiren Hizmetleri kaydetmek için.
  * `MapGrpcReflectionService` yansıma hizmeti uç noktası eklemek için.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>`grpcurl` komutunu kullanma

`-help`Bağımsız değişkeni `grpcurl` komut satırı seçeneklerini açıklar:

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Hizmetleri bulma

`describe`Sunucu tarafından tanımlanan hizmetleri görüntülemek için fiili ' i kullanın:

```powershell
> grpcurl.exe localhost:5001 describe
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
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>GRPC hizmetlerini çağırma

Bir hizmet ve Yöntem adı belirterek, istek iletisini temsil eden bir JSON bağımsız değişkeniyle birlikte bir gRPC hizmeti çağırın. JSON, Protoarabelleğe dönüştürülüp hizmete gönderilir.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Önceki örnek:

* `-d` bağımsız değişken JSON ile bir istek iletisi belirtir. Bu bağımsız değişken, sunucu adresi ve yöntem adından önce gelmelidir.
* `SayHello`Hizmeti üzerinde yöntemini çağırır `greeter.Greeter` .
* Yanıt iletisini JSON olarak yazdırır.

## <a name="about-grpcui"></a>Grpcuı hakkında

Grpcuı, gRPC için etkileşimli bir Web Kullanıcı arabirimi. Bu, Grpwrapper 'ın üzerinde oluşturulur ve, Postman gibi HTTP araçlarına benzer şekilde gRPC hizmetlerini keşfetmek ve test etmek için bir GUI sağlar.

İndirme ve yükleme hakkında bilgi için `grpcui` bkz. [Grpcuı GitHub ana sayfası](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>`grpcui` kullanma

`grpcui`Bir bağımsız değişken olarak etkileşimde bulunmak için sunucu adresiyle çalıştırın.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Araç etkileşimli Web Kullanıcı arabirimi ile bir tarayıcı penceresi başlatır. gRPC Hizmetleri, gRPC yansıması kullanılarak otomatik olarak bulunur.

![Grpcuı Web Kullanıcı arabirimi](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [Grpkıvrımlı GitHub giriş sayfası](https://github.com/fullstorydev/grpcurl)
* [Grpcuı GitHub giriş sayfası](https://github.com/fullstorydev/grpcui)
* [GRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
