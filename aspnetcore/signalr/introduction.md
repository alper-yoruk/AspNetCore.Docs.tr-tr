---
title: ASP.NET Core girişSignalR
author: bradygaster
description: ASP.NET Core SignalR kitaplığının uygulamalara gerçek zamanlı işlevsellik eklemeyi nasıl basitleştireceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022179"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>ASP.NET Core girişSignalR

## <a name="what-is-no-locsignalr"></a>Nedir SignalR ?

ASP.NET Core SignalR , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran açık kaynaklı bir kitaplıktır. Gerçek zamanlı Web işlevselliği, sunucu tarafı kodun anında istemcilere içerik gönderebilmesine olanak sağlar.

İçin iyi adaylar SignalR :

* Sunucudan yüksek sıklıkta güncelleştirmeler gerektiren uygulamalar. Oyun, sosyal ağlar, oylama, açık artırma, haritalar ve GPS uygulamaları bunlara örnektir.
* Panolar ve izleme uygulamaları. Şirket panoları, anlık satış güncelleştirmeleri veya seyahat uyarıları bunlara örnektir.
* İş birliği uygulamaları. Beyaz tahta uygulamaları ve takım toplantısı yazılımları, iş birliği uygulamalarına örnektir.
* Bildirim gerektiren uygulamalar. Sosyal ağlar, e-posta, sohbet, oyunlar, seyahat uyarıları ve diğer birçok uygulama, bildirimleri kullanır.

SignalRsunucudan istemciye [uzak yordam çağrıları (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)oluşturmak IÇIN bir API sağlar. RPC 'ler, sunucu tarafı .NET Core kodundan gelen istemcilerdeki JavaScript işlevlerini çağırır.

ASP.NET Core için bazı özellikler şunlardır SignalR :

* Bağlantı yönetimini otomatik olarak işler.
* Tüm bağlı istemcilere aynı anda iletiler gönderir. Örneğin, bir sohbet odası.
* Belirli istemcilere veya istemci gruplarına iletiler gönderir.
* Artan trafiği işleyecek şekilde ölçeklendirilir.

Kaynak [ SignalR GitHub 'daki bir depoda](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)barındırılır.

## <a name="transports"></a>Taşımalar

SignalRgerçek zamanlı iletişimi işlemek için aşağıdaki teknikleri destekler (düzgün geri dönüş sırasında):

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Sunucu tarafından gönderilen olaylar
* Uzun yoklama

SignalRsunucu ve istemci özellikleri içinde en iyi taşıma yöntemini otomatik olarak seçer.

## <a name="hubs"></a>Hub'lar

SignalRistemciler ve sunucular arasında iletişim kurmak için *hub 'ları* kullanır.

Hub, bir istemcinin ve sunucunun birbirlerine Yöntemler çağırmasını sağlayan üst düzey bir işlem hattdır. SignalRmakinenin sunucu sınırları genelinde dağıtımını otomatik olarak işler ve istemcilerin sunucuda Yöntemler çağırmasını sağlar ve tam tersi de geçerlidir. Model bağlamayı sağlayan yöntemlere kesin olarak yazılmış parametreler geçirebilirsiniz. SignalR, iki yerleşik hub protokolünü sağlar: JSON tabanlı bir metin Protokolü ve [MessagePack](https://msgpack.org/)temelli bir ikili protokol.  MessagePack genellikle JSON ile karşılaştırıldığında daha küçük iletiler oluşturur. Daha eski tarayıcıların, MessagePack protokolü desteği sağlamak için [XHR düzey 2](https://caniuse.com/#feat=xhr2) ' i desteklemesi gerekir.

Hub 'lar, istemci tarafı yönteminin adını ve parametrelerini içeren iletiler göndererek istemci tarafı kodu çağırır. Yöntem parametreleri olarak gönderilen nesneler, yapılandırılan protokol kullanılarak seri durumdan çıkarılacak. İstemci, adı istemci tarafı koddaki bir yöntemle eşleştirmeye çalışır. İstemci bir eşleşme bulduğunda, yöntemini çağırır ve seri durumdan çıkarılan parametre verilerine geçirir.

## <a name="additional-resources"></a>Ek kaynaklar

* [SignalRASP.NET Core için kullanmaya başlama](xref:tutorials/signalr)
* [Desteklenen platformlar](xref:signalr/supported-platforms)
* [Hub'lar](xref:signalr/hubs)
* [JavaScript istemcisi](xref:signalr/javascript-client)
