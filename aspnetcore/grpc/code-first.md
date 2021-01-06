---
title: .NET ile kod ilk gRPC Hizmetleri ve istemcileri
author: jamesnk
description: .NET ile Code-First gRPC yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880637"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>.NET ile kod ilk gRPC Hizmetleri ve istemcileri

, [James bAyKiNg](https://twitter.com/jamesnk) ve [Marc Gravell](https://twitter.com/marcgravell)

Code-First gRPC, hizmet ve ileti sözleşmelerini tanımlamak için .NET türlerini kullanır.

Kod-ilk bir sistem .NET kullandığında iyi bir seçimdir:

* .Net hizmeti ve veri sözleşmesi türleri .NET sunucusu ve istemcileri arasında paylaşılabilir.
* `.proto`Dosyalarda ve kod oluşturmada sözleşmeleri tanımlama ihtiyacını önler.

Kod-önce birden çok dile sahip çok yönlü sistemlerinde önerilmez. .NET hizmeti ve veri sözleşmesi türleri non-.NET platformlarıyla birlikte kullanılamaz. Önce kod kullanılarak yazılmış bir gRPC hizmetini çağırmak için, diğer platformların `.proto` hizmetle eşleşen bir sözleşme oluşturması gerekir.

## <a name="protobuf-netgrpc"></a>prototip-net. GRPC

> [!IMPORTANT]
> Prototipli-net için yardım. GRPC, [prototipli-net ' i ziyaret edin. GRPC Web sitesi](https://protobuf-net.github.io/protobuf-net.Grpc/) veya prototip-net üzerinde bir sorun oluşturun [. GRPC GitHub deposu](https://github.com/protobuf-net/protobuf-net.Grpc).

[prototip-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) bir topluluk projem ve Microsoft tarafından desteklenmez. Ve ' a ilk kod desteği ekler `Grpc.AspNetCore` `Grpc.Net.Client` . Uygulamanın gRPC hizmetlerini ve iletilerini tanımlamak için özniteliklerle açıklanmış .NET türlerini kullanır.

Kodu ilk gRPC hizmeti oluşturmaya yönelik ilk adım, kod sözleşmesini tanımlıyor:

* Sunucu ve istemci tarafından paylaşılacak yeni bir proje oluşturun.
* Prototip oluşturma [-net. GRPC](https://www.nuget.org/packages/protobuf-net.Grpc) paket başvurusu.
* Hizmet ve veri sözleşmesi türleri oluşturun.

[!code-csharp[](code-first/Contracts.cs)]

Yukarıdaki kod:

* `HelloRequest`Ve `HelloReply` iletilerini tanımlar.
* `IGreeterService`Birli GRPC yöntemiyle sözleşme arabirimini tanımlar `SayHelloAsync` .

Hizmet sözleşmesi sunucuda uygulanır ve istemciden çağırılır. Hizmet arabirimlerinde tanımlanan yöntemlerin tekil, sunucu akışı, istemci akışı veya çift yönlü akış olmasına bağlı olarak belirli imzalara eşleşmesi gerekir.

Hizmet sözleşmelerini tanımlama hakkında daha fazla bilgi için bkz [. prototipli-net. GRPC Başlarken belgeleri](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Kod oluştur-ilk gRPC hizmeti

GRPC kodu-ilk hizmetini bir ASP.NET Core uygulamasına eklemek için:

* Prototip oluşturma [-net. GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) paket başvurusu.
* Paylaşılan kod-sözleşme projesine bir başvuru ekleyin.

Yeni bir `GreeterService.cs` dosya oluşturun ve `IGreeterService` hizmet arabirimini uygulayın:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Dosyayı güncelleştirin `Startup.cs` :

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

Yukarıdaki kodda:

* `AddCodeFirstGrpc` önce kodu etkinleştiren Hizmetleri kaydeder.
* `MapGrpcService<GreeterService>` kod ilk hizmet uç noktasını ekler.

yalnızca kodla uygulanan gRPC Hizmetleri ve `.proto` dosyalar aynı uygulamada birlikte bulunabilir. Tüm gRPC Hizmetleri [GRPC hizmeti yapılandırmasını](xref:grpc/configuration#configure-services-options)kullanır.

## <a name="create-a-code-first-grpc-client"></a>Kod oluştur-ilk gRPC istemcisi

Bir kod-ilk gRPC istemcisi, gRPC hizmetlerini çağırmak için hizmet sözleşmesini kullanır. Bir gRPC hizmetini kod ilk istemcisini kullanarak çağırmak için:

* Prototip oluşturma [-net. GRPC](https://www.nuget.org/packages/protobuf-net.Grpc) paket başvurusu.
* Paylaşılan kod-sözleşme projesine bir başvuru ekleyin.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Yukarıdaki kod:

* Bir gRPC kanalı oluşturur.
* Uzantı yöntemiyle kanaldan bir kod ilk istemci oluşturur `CreateGrpcService<IGreeterService>` .
* İle gRPC hizmetini çağırır `SayHelloAsync` .

Bir kanaldan kod ilk gRPC istemcisi oluşturulur. Normal bir istemcide olduğu gibi, bir kod ilk istemcisi de kendi [Kanal yapılandırmasını](xref:grpc/configuration#configure-client-options)kullanır.

## <a name="additional-resources"></a>Ek kaynaklar

* [prototip-net. GRPC Web sitesi](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [prototip-net. GRPC GitHub deposu](https://github.com/protobuf-net/protobuf-net.Grpc)
