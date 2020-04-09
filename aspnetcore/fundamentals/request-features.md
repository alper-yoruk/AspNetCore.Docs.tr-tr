---
title: ASP.NET Core'daki İstek Özellikleri
author: ardalis
description: ASP.NET Core arabirimlerinde tanımlanan HTTP istekleri ve yanıtları ile ilgili web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416228"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core'daki İstek Özellikleri

Yazar: [Steve Smith](https://ardalis.com/)

HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları arabirimlerde tanımlanır. Bu arabirimler, sunucu uygulamaları ve ara yazılımlar tarafından uygulamanın barındırma ardışık hattını oluşturmak ve değiştirmek için kullanılır.

## <a name="feature-interfaces"></a>Özellik arabirimleri

ASP.NET Core, sunucular `Microsoft.AspNetCore.Http.Features` tarafından destekledikleri özellikleri tanımlamak için kullanılan bir dizi HTTP özellik arabirimi tanımlar. Aşağıdaki özellik arabirimleri istekleri ve yanıtları döndürün:

`IHttpRequestFeature`Protokol, yol, sorgu dizesi, üstbilgi ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar.

`IHttpResponseFeature`Durum kodu, üstbilgi ve yanıtın gövdesi de dahil olmak üzere bir HTTP yanıtının yapısını tanımlar.

`IHttpAuthenticationFeature`Kullanıcıları bir `ClaimsPrincipal` temele göre tanımlamak ve kimlik doğrulaması belirleyen desteği tanımlar.

`IHttpUpgradeFeature`Sunucu protokolleri değiştirmek isterse hangi ek protokolleri kullanmak istediğini belirtmek için istemciye izin veren [HTTP Yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)desteği tanımlar.

`IHttpBufferingFeature`İstek lerin ve/veya yanıtların arabelleğe alma yöntemlerini tanımlar.

`IHttpConnectionFeature`Yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.

`IHttpRequestLifetimeFeature`Bağlantıların iptali veya istemci bağlantısının kesilmesi gibi bir isteğin zamanından önce sonlandırıldığını algılama desteği tanımlar.

`IHttpSendFileFeature`Dosyaları eşit bir şekilde göndermek için bir yöntem tanımlar.

`IHttpWebSocketFeature`Web yuvalarını desteklemek için bir API tanımlar.

`IHttpRequestIdentifierFeature`İstekleri benzersiz olarak tanımlamak için uygulanabilecek bir özellik ekler.

`ISessionFeature`Kullanıcı `ISessionFactory` oturumlarını desteklemek için tanımlar ve `ISession` soyutlamalar.

`ITlsConnectionFeature`İstemci sertifikalarını almak için bir API tanımlar.

`ITlsTokenBindingFeature`TLS belirteç bağlama parametreleri ile çalışma yöntemlerini tanımlar.

> [!NOTE]
> `ISessionFeature`bir sunucu özelliği değildir, ancak `SessionMiddleware` (bkz. [Uygulama Durumunu Yönetme)](app-state.md)tarafından uygulanır.

## <a name="feature-collections"></a>Özellik koleksiyonları

Özelliği, `Features` `HttpContext` geçerli istek için kullanılabilir HTTP özelliklerini almak ve ayarlamak için bir arabirim sağlar. Özellik koleksiyonu bir istek bağlamında bile tustubl olduğundan, ara yazılım koleksiyonu değiştirmek ve ek özellikler için destek eklemek için kullanılabilir.

## <a name="middleware-and-request-features"></a>Ara yazılım ve istek özellikleri

Sunucular özellik koleksiyonunu oluşturmaktan sorumlu olsa da, ara yazılımlar hem bu koleksiyona ekleyebilir hem de koleksiyondaki özellikleri tüketebilir. Örneğin, `IHttpSendFileFeature` özellik `StaticFileMiddleware` erişir. Özellik varsa, istenen statik dosyayı fiziksel yolundan göndermek için kullanılır. Aksi takdirde, dosyayı göndermek için daha yavaş bir alternatif yöntem kullanılır. Kullanılabilir olduğunda, `IHttpSendFileFeature` işletim sisteminin dosyayı açmasına ve ağ kartına doğrudan çekirdek modu kopyasını gerçekleştirmesine olanak tanır.

Ayrıca, ara yazılım sunucu tarafından kurulan özellik koleksiyonuna ekleyebilirsiniz. Mevcut özellikler, ara yazılımın sunucunun işlevselliğini genişletmesine olanak tanıyan ara yazılımlarla bile değiştirilebilir. Koleksiyona eklenen özellikler, istek ardışık alanında hemen diğer ara yazılımlara veya altta yatan uygulamanın kendisine kullanılabilir.

Özel sunucu uygulamaları ve belirli ara yazılım geliştirmeleri birleştirilerek, bir uygulamanın gerektirdiği kesin özellik kümesi oluşturulabilir. Bu, eksik özelliklerin sunucuda değişiklik gerektirmeden eklenmesine olanak tanır ve yalnızca en az sayıda özelliğin açığa çıkarolmasını sağlayarak saldırı yüzey alanını sınırlandırarak performansı artırır.

## <a name="summary"></a>Özet

Özellik arabirimleri, belirli bir isteğin desteklenebilen belirli HTTP özelliklerini tanımlar. Sunucular, özellik koleksiyonlarını ve bu sunucu tarafından desteklenen ilk özellik kümesini tanımlar, ancak bu özellikleri geliştirmek için ara yazılımkullanılabilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Sunucular](xref:fundamentals/servers/index)
* [Ara yazılım](xref:fundamentals/middleware/index)
* [.NET için Açık Web Arabirimi (OWIN)](xref:fundamentals/owin)
