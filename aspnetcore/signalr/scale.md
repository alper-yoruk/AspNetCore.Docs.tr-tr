---
title: ASP.NET Core SignalR Üretim barındırma ve ölçeklendirme
author: bradygaster
description: ASP.NET Core kullanan uygulamalardaki performans ve ölçeklendirme sorunlarının nasıl önleneceğini öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: 2d128d54dc9b1189124563e45d72d74b19704ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022530"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a>SignalRBarındırma ve ölçeklendirmeyi ASP.NET Core

, [Andrew Stanton-nuri](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)ve [Tom Dykstra](https://github.com/tdykstra),

Bu makalede, ASP.NET Core kullanan yüksek trafik uygulamalarına yönelik barındırma ve ölçeklendirme konuları açıklanmaktadır SignalR .

## <a name="sticky-sessions"></a>Yapışkan oturumlar

SignalRbelirli bir bağlantı için tüm HTTP isteklerinin aynı sunucu işlemi tarafından işlenmesini gerektirir. SignalRBir sunucu grubunda (birden çok sunucu) çalışırken, "yapışkan oturumlar" kullanılması gerekir. "Yapışkan oturumlar", bazı yük dengeleyiciler tarafından oturum benzeşimi olarak da adlandırılır. Azure App Service istekleri yönlendirmek için [uygulama Isteği yönlendirme](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) kullanır. Azure App Service "ARR benzeşimi" ayarının etkinleştirilmesi "yapışkan oturumlar" sağlayacaktır. Yapışkan oturumların gerekmediği tek koşullar şunlardır:

1. Tek bir sunucuda barındırırken, tek bir işlemde.
1. Azure SignalR hizmetini kullanırken.
1. Tüm istemciler **yalnızca** WebSockets kullanacak şekilde yapılandırıldığında **ve** istemci yapılandırmasında [skipanlaşma ayarı](xref:signalr/configuration#configure-additional-options) etkinse.

Tüm diğer koşullarda (Redsıs geri düzlemi kullanıldığında dahil), sunucu ortamının yapışkan oturumlar için yapılandırılması gerekir.

İçin Azure App Service yapılandırma kılavuzu için SignalR , bkz <xref:signalr/publish-to-azure-web-app> ..

## <a name="tcp-connection-resources"></a>TCP bağlantı kaynakları

Bir Web sunucusunun destekleyebileceği eşzamanlı TCP bağlantısı sayısı sınırlıdır. Standart HTTP istemcileri, *kısa ömürlü* bağlantıları kullanır. Bu bağlantılar, istemci boşta kaldığında ve daha sonra yeniden açıldığı zaman kapatılabilir. Öte yandan bir SignalR bağlantı *kalıcıdır*. SignalRistemci boşta kaldığında bile bağlantılar açık kalır. Çok sayıda istemciye hizmet veren yüksek trafikli bir uygulamada, bu kalıcı bağlantılar sunucuların en fazla bağlantı sayısına ulaşmasına neden olabilir.

Kalıcı bağlantılar, her bağlantıyı izlemek için ek bellek de tüketir.

İle bağlantıyla ilgili kaynakların ağır kullanımı, SignalR aynı sunucuda barındırılan diğer Web uygulamalarını etkileyebilir. SignalR, Ve en son KULLANILABILIR TCP bağlantılarını tutuyorsa, aynı sunucudaki diğer Web uygulamalarına da daha fazla bağlantı bulunmaz.

Sunucuda bağlantı biterse rastgele yuva hataları ve bağlantı sıfırlama hataları görürsünüz. Örnek:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

SignalRKaynak kullanımının diğer Web uygulamalarındaki hatalara neden olmasına devam etmek için SignalR diğer Web uygulamalarınızdan farklı sunucular üzerinde çalıştırın.

SignalRKaynak kullanımını bir uygulamadaki hatalara neden olmasına devam etmek için SignalR , bir sunucunun işlemesi olan bağlantı sayısını sınırlamak üzere ölçeği ölçeklendirin.

## <a name="scale-out"></a>Ölçeği genişletme

Kullanan bir uygulamanın SignalR , bir sunucu grubu için sorunlar oluşturan tüm bağlantılarını izlemesi gerekir. Bir sunucu ekleyin ve diğer sunucuların hakkında bilgi sahibi olmadığı yeni bağlantıları alır. Örneğin, SignalR aşağıdaki diyagramdaki her bir sunucuda diğer sunuculardaki bağlantılar farkında değildir. SignalRSunuculardan birinde tüm istemcilere ileti göndermek istediğinde ileti yalnızca bu sunucuya bağlı olan istemcilere gider.

![Ölçeklendirme::: No-Loc (SignalR)::: geri düzlemi olmadan](scale/_static/scale-no-backplane.png)

Bu sorunu çözmeye yönelik seçenekler [Azure SignalR hizmeti](#azure-signalr-service) ve [Redsıs arkadüzledir](#redis-backplane).

## <a name="azure-no-locsignalr-service"></a>Azure SignalR hizmeti

Azure SignalR hizmeti, arka düzlemi yerine bir ara sunucu. İstemci sunucuya bir bağlantı başlattığında, istemci hizmete bağlanmak için yeniden yönlendirilir. Bu işlem aşağıdaki diyagramda gösterilmiştir:

![Azure::: No-Loc (SignalR)::: Service bağlantısı kuruluyor](scale/_static/azure-signalr-service-one-connection.png)

Sonuç olarak, hizmetin tüm istemci bağlantılarını yönetmesi, her sunucunun aşağıdaki diyagramda gösterildiği gibi yalnızca küçük bir sabit bağlantı sayısına ihtiyacı vardır:

![Hizmete bağlı istemciler, hizmete bağlı sunucular](scale/_static/azure-signalr-service-multiple-connections.png)

Bu genişleme yaklaşımına yönelik bu yaklaşım, Redsıs geri düzlemi 'nin diğer avantajlarından biridir:

* [İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar gerekli değildir, çünkü Istemciler bağlandıklarında Azure hizmetine anında yönlendirilir SignalR .
* SignalRAzure SignalR hizmeti, herhangi bir sayıda bağlantıyı işleyecek şekilde otomatik olarak ölçeklendirilirken, gönderilen ileti sayısına göre bir uygulamanın ölçeği ölçeklenebilir. Örneğin, binlerce istemci olabilir, ancak saniye başına yalnızca birkaç ileti gönderiliyorsa, SignalR uygulamanın yalnızca bağlantıları işlemek için birden çok sunucuya ölçeklendirilmesi gerekmez.
* Bir SignalR uygulama, olmadan bir Web uygulamasından çok daha fazla bağlantı kaynağı kullanmaz SignalR .

Bu nedenlerden dolayı, SignalR SignalR App Service, VM 'ler ve kapsayıcılar dahil olmak üzere Azure 'da barındırılan tüm ASP.NET Core uygulamaları için Azure hizmetini öneririz.

Daha fazla bilgi için bkz. [Azure SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis kartı

[Redsıs](https://redis.io/) , bir yayımlama/abonelik modeliyle bir mesajlaşma sistemini destekleyen bellek içi anahtar-değer deposudur. SignalRRedsıs geri düzlemi, iletileri diğer sunuculara iletmek için pub/Sub özelliğini kullanır. İstemci bir bağlantı yaptığında, bağlantı bilgileri geri düzleme geçirilir. Bir sunucu tüm istemcilere ileti göndermek istediğinde, bu geri düzlemi gönderir. Biriktirme listesi, tüm bağlı istemcileri ve bunların hangi sunuculara olduğunu bilir. İletiyi ilgili sunucuları aracılığıyla tüm istemcilere gönderir. Bu işlem aşağıdaki diyagramda gösterilmiştir:

![Redsıs geri düzlemi, bir sunucudan tüm istemcilere gönderilen ileti](scale/_static/redis-backplane.png)

Redsıs geri düzlemi, kendi altyapınızda barındırılan uygulamalar için önerilen genişleme yaklaşımıdır. Veri merkezinizde bir Azure veri merkezi arasında önemli bir bağlantı gecikmesi varsa, Azure SignalR hizmeti düşük gecikme süresi veya yüksek performans gereksinimlerine sahip şirket içi uygulamalar için pratik bir seçenek olmayabilir.

SignalRDaha önce bahsedilen Azure hizmeti avantajları redsıs geri düzlemi için dezavantajlardır:

* [İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar, aşağıdakilerin **her ikisi** de doğru olduğunda gereklidir:
  * Tüm istemciler **yalnızca** WebSockets kullanacak şekilde yapılandırılır.
  * [Skipanlaşma ayarı](xref:signalr/configuration#configure-additional-options) istemci yapılandırmasında etkindir. 
   Sunucuda bir bağlantı başlatıldıktan sonra bağlantı o sunucuda kalmaya devam etmek zorunda kalır.
* SignalRBirkaç ileti gönderilse bile, bir uygulamanın, istemci sayısına göre ölçeği ölçeklendirilmesi gerekir.
* Bir SignalR uygulama, olmadan bir Web uygulamasından çok daha fazla bağlantı kaynağı kullanır SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>Windows istemci işletim sisteminde IIS sınırlamaları

Windows 10 ve Windows 8. x istemci işletim sistemleridir. İstemci işletim sistemlerindeki IIS, 10 eşzamanlı bağlantı sınırına sahiptir. SignalRbağlantıları şunlardır:

* Geçici ve sık yeniden oluşturuldu.
* Artık **kullanılmadıysa** hemen atılamaz.

Yukarıdaki koşullar, istemci işletim sistemi üzerindeki 10 bağlantı sınırına ulaşmaları olasılığını ortadan alır. Geliştirme için bir istemci işletim sistemi kullanıldığında şunları yapmanızı öneririz:

* IIS kullanmaktan kaçının.
* Dağıtım hedefleri olarak Kestrel veya IIS Express kullanın.

## <a name="linux-with-nginx"></a>Nginx ile Linux

Proxy 'nin `Connection` ve `Upgrade` üst bilgilerini WebSockets için aşağıdaki şekilde ayarlayın SignalR :

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Daha fazla bilgi için bkz. [WebSocket proxy 'si olarak NGINX](https://www.nginx.com/blog/websocket-nginx/).

## <a name="third-party-no-locsignalr-backplane-providers"></a>Üçüncü taraf SignalR arka düzlem sağlayıcıları

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-overview)
* [Redsıs geri düzlemi ayarlama](xref:signalr/redis-backplane)
