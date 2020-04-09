---
title: C# içeren gRPC hizmetleri
author: juntaoluo
description: C# ile gRPC hizmetleri yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664208"
---
# <a name="grpc-services-with-c"></a>C ile gRPC hizmetleri\#

Bu belge, C#'a [gRPC](https://grpc.io/docs/guides/) uygulamaları yazmak için gereken kavramları özetler. Burada ele alınan konular hem [C-çekirdek](https://grpc.io/blog/grpc-stacks)tabanlı hem de ASP.NET Core tabanlı gRPC uygulamaları için geçerlidir.

## <a name="proto-file"></a>proto dosyası

gRPC, API geliştirme için sözleşmeye ilk yaklaşım kullanır. Protokol arabellekleri (protobuf) varsayılan olarak Arabirim Tasarım Dili (IDL) olarak kullanılır. .proto dosyası şunları içerir: * \**

* gRPC hizmetinin tanımı.
* İstemciler ve sunucular arasında gönderilen iletiler.

Protobuf dosyalarının sözdizimi hakkında daha fazla bilgi için [resmi belgelere (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)bakın.

Örneğin, [gRPC hizmeti ile başlayın'da](xref:tutorials/grpc/grpc-start)kullanılan *greet.proto* dosyasını göz önünde bulundurun:

* Bir `Greeter` hizmeti tanımlar.
* Hizmet `Greeter` bir `SayHello` çağrı tanımlar.
* `SayHello`ileti `HelloRequest` gönderir ve `HelloReply` bir ileti alır:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>C\# uygulamasına .proto dosyası ekleme

.proto `<Protobuf>` dosyası, öğe grubuna eklenerek projeye dahil edilir: * \**

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C# .proto dosyaları için araçlama desteği

Takım paketi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) * \*.proto* dosyaları C# varlıkları oluşturmak için gereklidir. Oluşturulan varlıklar (dosyalar):

* Proje her oluşturulduğunda gerektiği gibi oluşturulur.
* Projeye eklenmez veya kaynak denetimine işaretlenmez.
* *Obj* dizininde bulunan bir yapı artefakt vardır.

Bu paket hem sunucu hem de istemci projeleri tarafından gereklidir. Metapaket `Grpc.AspNetCore` bir başvuru `Grpc.Tools`içerir . Sunucu projeleri `Grpc.AspNetCore` Visual Studio'daki Paket Yöneticisi'ni `<PackageReference>` kullanarak veya proje dosyasına bir ekleyerek ekleyebilir:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

İstemci projeleri, gRPC istemcisini kullanmak için gereken diğer paketlerin yanında doğrudan başvurulmalıdır. `Grpc.Tools` Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`aşağıdakilerle işaretlenir:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Oluşturulan C# varlıkları

Araç paketi, dahil * \*edilen .proto* dosyalarında tanımlanan iletileri temsil eden C# türlerini oluşturur.

Sunucu tarafındaki varlıklar için soyut bir hizmet taban türü oluşturulur. Temel *tür,.proto* dosyasında bulunan tüm gRPC çağrılarının tanımlarını içerir. Bu taban türünden türeyen ve gRPC çağrıları için mantığı uygulayan somut bir hizmet uygulaması oluşturun. `greet.proto`Daha önce açıklanan örnek için, `GreeterBase` sanal `SayHello` bir yöntem içeren soyut bir tür oluşturulur. Somut bir `GreeterService` uygulama yöntemi geçersiz kılar ve gRPC çağrısını işleme mantığını uygular.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

İstemci tarafındaki varlıklar için somut bir istemci türü oluşturulur. *.proto* dosyasındaki gRPC çağrıları, çağrılabilecek somut türdeki yöntemlere dönüştürülr. `greet.proto`Daha önce açıklanan örnek için somut `GreeterClient` bir tür oluşturulur. Sunucuya gRPC çağrısı başlatmak için arayın. `GreeterClient.SayHelloAsync`

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Varsayılan olarak, `<Protobuf>` madde grubuna dahil edilen her * \*.proto* dosyası için sunucu ve istemci varlıkları oluşturulur. Bir sunucu projesinde yalnızca sunucu varlıklarının `GrpcServices` oluşturulduğundan emin `Server`olmak için öznitelik .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Benzer şekilde, öznitelik istemci `Client` projelerinde ayarlanır.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
