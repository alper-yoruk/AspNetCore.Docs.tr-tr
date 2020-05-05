---
title: ASP.NET Core içindeki istek özellikleri
author: ardalis
description: ASP.NET Core arabirimlerde tanımlanan HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: e26a1a7b35d40c1214bbc40269571545cbd2c235
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776031"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core içindeki istek özellikleri

[Steve Smith](https://ardalis.com/) tarafından

HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları arabirimlerde tanımlanmıştır. Bu arabirimler, uygulamanın barındırma işlem hattını oluşturmak ve değiştirmek için sunucu uygulamaları ve ara yazılım tarafından kullanılır.

## <a name="feature-interfaces"></a>Özellik arabirimleri

ASP.NET Core, `Microsoft.AspNetCore.Http.Features` sunucularının destekledikleri özellikleri tanımlamak için kullandıkları BIR dizi http özelliği arabirimini tanımlar. Aşağıdaki özellik arabirimleri istekleri ve dönüş yanıtlarını işler:

`IHttpRequestFeature`Protokol, yol, sorgu dizesi, üst bilgiler ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar.

`IHttpResponseFeature`Durum kodu, üst bilgiler ve yanıtın gövdesi dahil olmak üzere bir HTTP yanıtının yapısını tanımlar.

`IHttpAuthenticationFeature`, Ve bir `ClaimsPrincipal` kimlik doğrulama işleyicisi belirterek kullanıcıları tanımlama desteğini tanımlar.

`IHttpUpgradeFeature`Sunucu, protokolleri değiştirmek isterse, istemcinin kullanmak istediğiniz ek protokolleri belirtmesini sağlayan [http yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)için desteği tanımlar.

`IHttpBufferingFeature`İsteklerin ve/veya yanıtlarının arabelleğe alınması devre dışı bırakma yöntemlerini tanımlar.

`IHttpConnectionFeature`Yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.

`IHttpRequestLifetimeFeature`Bağlantıların iptal edilme desteğini tanımlar veya bir isteğin erken sonlandırılıp sonlandırılmayacağını (örneğin, bir istemci bağlantısı kesildiğini) belirler.

`IHttpSendFileFeature`Dosyaları zaman uyumsuz olarak göndermek için bir yöntem tanımlar.

`IHttpWebSocketFeature`Web yuvalarını desteklemek için bir API tanımlar.

`IHttpRequestIdentifierFeature`İstekleri benzersiz şekilde tanımlamak için uygulanabilecek bir özellik ekler.

`ISessionFeature`Kullanıcı `ISessionFactory` oturumlarını `ISession` desteklemek için tanımlar ve soyutlamalar tanımlar.

`ITlsConnectionFeature`İstemci sertifikalarını almak için bir API tanımlar.

`ITlsTokenBindingFeature`TLS belirteci bağlama parametreleriyle çalışma yöntemlerini tanımlar.

> [!NOTE]
> `ISessionFeature`bir sunucu özelliği değildir, ancak `SessionMiddleware` (bkz. [uygulama durumunu yönetme](app-state.md)) tarafından uygulanır.

## <a name="feature-collections"></a>Özellik koleksiyonları

Özelliği `Features` , geçerli `HttpContext` istek için kullanılabilir http özelliklerini almak ve ayarlamak için bir arabirim sağlar. Özellik koleksiyonu bir istek bağlamı içinde bile değişebilir olduğundan, ara yazılım, koleksiyonu değiştirmek ve ek özellikler için destek eklemek üzere kullanılabilir.

## <a name="middleware-and-request-features"></a>Ara yazılım ve istek özellikleri

Sunucular Özellik koleksiyonu oluşturmaktan sorumludur, ancak ara yazılım bu koleksiyona ekleyebilir ve koleksiyondaki özellikleri kullanabilir. Örneğin, `StaticFileMiddleware` `IHttpSendFileFeature` özelliğine erişir. Özellik varsa, istenen statik dosyayı fiziksel yolundan göndermek için kullanılır. Aksi takdirde, dosyayı göndermek için daha yavaş bir alternatif yöntem kullanılır. Kullanılabilir olduğunda, işletim `IHttpSendFileFeature` sisteminin dosyayı açmasına ve ağ kartına doğrudan bir çekirdek modu kopyalaması gerçekleştirmesine izin verir.

Ayrıca, ara yazılım, sunucu tarafından belirlenen özellik koleksiyonuna eklenebilir. Mevcut özellikler, ara yazılım tarafından da değiştirilebilir ve bu da ara yazılım, sunucunun işlevselliğini artırabilir. Koleksiyona eklenen özellikler, daha sonra istek ardışık düzeninde bulunan diğer ara yazılım veya temeldeki uygulamanın kendisi için de kullanılabilir.

Özel sunucu uygulamalarını ve belirli ara yazılım geliştirmelerini birleştirerek, bir uygulamanın gerektirdiği kesin özellikler kümesi oluşturulabilir. Bu, sunucuda değişiklik gerektirmeden eksik özelliklerin eklenmesine izin verir ve yalnızca en düşük miktarda özelliğin gösterilmesini sağlar, böylece saldırı yüzeyi alanını kısıtlar ve performansı geliştirir.

## <a name="summary"></a>Özet

Özellik arabirimleri, belirli bir isteğin destekleyebileceği belirli HTTP özelliklerini tanımlar. Sunucular, özellik koleksiyonlarını ve bu sunucu tarafından desteklenen ilk özellik kümesini tanımlar, ancak bu özellikleri geliştirmek için ara yazılım kullanılabilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Sunucular](xref:fundamentals/servers/index)
* [Ara yazılım](xref:fundamentals/middleware/index)
* [.NET için Açık Web Arabirimi (OWIN)](xref:fundamentals/owin)
