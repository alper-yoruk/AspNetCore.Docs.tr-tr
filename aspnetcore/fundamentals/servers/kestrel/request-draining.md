---
title: ASP.NET Core Kestrel Web sunucusu ile boşaltma isteği
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel ile istek boşaltma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253980"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel Web sunucusu ile boşaltma isteği

HTTP bağlantılarının açılması zaman alabilir. HTTPS için de kaynak kullanımı yoğun bir işlemdir. Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır. Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir. Uygulama, sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü HTTP POST istekleri gibi istek gövdesini her zaman tüketmez. HTTP yeniden yönlendirme durumunda:

* İstemci, POST verilerinin bir bölümünü zaten göndermiş olabilir.
* Sunucu 301 yanıtını yazar.
* Önceki istek gövdesinden POST verileri tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.
* Kestrel, istek gövdesini boşaltmaya çalışır. İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.

Boşaltma işlemi, bağlantının yeniden kullanılmasına izin verilmesi ve kalan verilerin boşaltımı için geçen süre arasında bir zorunluluğunu getirir oluşturur:

* Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.
* Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.

Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz. Örneğin, istemciler sınırlı veri üst sınırı içerebilir. Karşıya yüklenen verileri sınırlama bir öncelik olabilir. Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .

Çağırmanın uyarıları vardır `Abort` :

* Yeni bağlantı oluşturma yavaş ve pahalı olabilir.
* İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.
* Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.
  * Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın. Örneğin, `Abort` kötü amaçlı istemciler VERI göndermeye çalışıyorsa veya istemci kodunda büyük veya çok sayıda istek oluşmasına neden olan bir hata olduğunda çağırın.
  * `Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.

Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar. Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.

Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler. Beş saniyelik boşaltma zaman aşımı uygulanmaz. Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir. Ek istek gövdesi veri çerçeveleri yok sayılır.

Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanması ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir. Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.
