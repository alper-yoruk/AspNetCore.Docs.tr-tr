---
title: BlazorBarındırma modellerini ASP.NET Core
author: guardrex
description: BlazorWebassembly ve Blazor sunucu barındırma modellerini anlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: a5323534cd76cfb60008636066ca5dcb7308d134
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102271"
---
# <a name="aspnet-core-blazor-hosting-models"></a>BlazorBarındırma modellerini ASP.NET Core

[Daniel Roth](https://github.com/danroth27) tarafından

Blazor, bir [Webassembly](https://webassembly.org/)tabanlı .NET çalışma zamanı (* Blazor webassembly*) veya ASP.NET Core (* Blazor sunucu*) içindeki sunucu tarafında tarayıcıda istemci tarafı çalıştıracak şekilde tasarlanan bir Web çerçevesidir. Barındırma modelinden bağımsız olarak, uygulama ve bileşen modelleri *aynıdır*.

Bu makalede açıklanan barındırma modelleriyle ilgili bir proje oluşturmak için, bkz <xref:blazor/get-started> ..

Gelişmiş yapılandırma için bkz <xref:blazor/fundamentals/configuration> ..

## <a name="blazor-webassembly"></a>BlazorWebAssembly

İçin sorumlu barındırma modeli, Blazor WebAssembly üzerinde tarayıcıda istemci tarafı çalıştırıyor. BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı tarayıcıya indirilir. Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür. UI güncelleştirmeleri ve olay işleme aynı işlem içinde oluşur. Uygulamanın varlıkları, istemcilere statik içerik sunan bir Web sunucusuna veya hizmete statik dosyalar olarak dağıtılır.

![BlazorWebAssembly: Blazor uygulama, tarayıcı içindeki bir kullanıcı arabirimi iş parçacığında çalışır.](hosting-models/_static/blazor-webassembly.png)

Blazorİstemci tarafı barındırma modelini kullanarak bir uygulama oluşturmak için ** Blazor Webassembly uygulama** şablonunu ([DotNet New blazorwasm](/dotnet/core/tools/dotnet-new)) kullanın.

** Blazor Webassembly uygulama** şablonunu seçtikten sonra, **ASP.NET Core barındırılan** onay kutusunu ([DotNet New blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) seçerek uygulamayı ASP.NET Core arka ucunu kullanacak şekilde yapılandırma seçeneğiniz vardır. ASP.NET Core uygulaması, Blazor uygulamayı istemcilere sunar. BlazorWebassembly uygulaması, Web API çağrılarını veya () kullanarak ağ üzerinden sunucu ile etkileşime geçebilir [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor-webassembly> .

Şablonlar aşağıdakileri `blazor.webassembly.js` işleyecek betiği içerir:

* .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirme.
* Uygulamayı çalıştırmak için çalışma zamanının başlatılması.

BlazorWebassembly barındırma modeli çeşitli avantajlar sunar:

* .NET sunucu tarafı bağımlılığı yoktur. Uygulama istemciye indirildikten sonra tam olarak çalışır.
* İstemci kaynakları ve yetenekleri tamamen yararlanılabilir.
* İş sunucudan istemciye boşaltılır.
* Uygulamayı barındırmak için bir ASP.NET Core Web sunucusu gerekli değildir. Sunucusuz dağıtım senaryoları mümkündür (örneğin, bir CDN 'den uygulama sunma).

Webassembly barındırması için küçük bir kenar vardır Blazor :

* Uygulama tarayıcının özelliklerine kısıtlıdır.
* Uyumlu istemci donanımı ve yazılımı (örneğin, WebAssembly desteği) gereklidir.
* İndirme boyutu daha büyüktür ve uygulamaların yüklenmesi daha uzun sürer.
* .NET çalışma zamanı ve araç desteği daha az olgun. Örneğin, [.NET Standard](/dotnet/standard/net-standard) desteğinin ve hata ayıklamada sınırlamalar mevcuttur.

BlazorBarındırılan uygulama modeli [Docker kapsayıcılarını](/dotnet/standard/microservices-architecture/container-docker-introduction/index)destekler. Visual Studio 'da sunucu projesine sağ tıklayın ve **Add**  >  **Docker desteği**Ekle ' yi seçin.

## <a name="blazor-server"></a>BlazorServer

BlazorSunucu barındırma modeliyle, uygulama sunucuda ASP.NET Core bir uygulama içinden yürütülür. Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .

![Tarayıcı, bir bağlantı üzerinden sunucusunda (bir ASP.NET Core uygulamasının içinde barındırılan) uygulamayla etkileşime girer SignalR .](hosting-models/_static/blazor-server.png)

BlazorSunucu barındırma modelini kullanarak bir uygulama oluşturmak için Blazor ASP.NET Core ** Blazor sunucusu uygulaması** şablonunu ([DotNet New blazorserver](/dotnet/core/tools/dotnet-new)) kullanın. ASP.NET Core uygulaması, Blazor sunucu uygulamasını barındırır ve SignalR istemcilerin bağlanacağı uç noktayı oluşturur.

ASP.NET Core uygulama, eklenecek uygulamanın sınıfına başvurur `Startup` :

* Sunucu tarafı hizmetler.
* İstek işleme işlem hattının uygulaması.

`blazor.server.js`Komut dosyası, istemci bağlantısını belirler. Uygulamanın, uygulama durumunu (örneğin, kayıp ağ bağlantısı durumunda) kalıcı hale getirmek ve geri yüklemek, uygulamanın sorumluluğundadır. `blazor.server.js`Betik, ASP.NET Core paylaşılan çerçevede eklenmiş bir kaynaktan sunulur.

BlazorSunucu barındırma modeli çeşitli avantajlar sunar:

* İndirme boyutu bir Blazor webassembly uygulamasından önemli ölçüde küçüktür ve uygulama çok daha hızlı bir şekilde yüklenir.
* Uygulama, .NET Core ile uyumlu API 'lerin kullanımı dahil olmak üzere sunucu olanaklarından tam olarak yararlanır.
* Sunucuda .NET Core, uygulamayı çalıştırmak için kullanılır, bu nedenle hata ayıklama gibi mevcut .NET araçları beklendiği gibi çalışır.
* Ölçülü istemciler desteklenir. Örneğin, Blazor sunucu uygulamaları, WebAssembly ve kaynak kısıtlı cihazlarda bulunan tarayıcılarla çalışır.
* Uygulamanın bileşen kodu da dahil olmak üzere, uygulamanın .NET/C# kod tabanı istemcilere sunulmuyor.

Sunucu barındırma için aşağı taraf vardır Blazor :

* Daha yüksek gecikme süresi genellikle vardır. Her Kullanıcı etkileşimi bir ağ atmasını içerir.
* Çevrimdışı destek yoktur. İstemci bağlantısı başarısız olursa, uygulama çalışmayı durduruyor.
* Ölçeklenebilirlik, çok sayıda kullanıcısı olan uygulamalar için zorlayıcı bir uygulamalardır. Sunucunun birden çok istemci bağlantısını yönetmesi ve istemci durumunu işlemesi gerekir.
* Uygulamayı çalıştırmak için bir ASP.NET Core sunucusu gerekir. Sunucusuz dağıtım senaryoları mümkün değildir (örneğin, bir CDN 'den uygulama sunma).

BlazorSunucu uygulama modeli [Docker kapsayıcılarını](/dotnet/standard/microservices-architecture/container-docker-introduction/index)destekler. Visual Studio 'da projeye sağ tıklayın ve **Add**  >  **Docker desteği**Ekle ' yi seçin.

### <a name="comparison-to-server-rendered-ui"></a>Sunucu tarafından işlenmiş Kullanıcı arabirimine karşılaştırma

Sunucu uygulamalarını anlamanın bir yolu Blazor , Razor görünümleri veya sayfaları kullanarak ASP.NET Core uygulamalarda Kullanıcı arabirimi oluşturma için geleneksel modellerden nasıl farklılık gösterir Razor . Her iki model de Razor HTML içeriğini anlatmak için dili kullanır, ancak biçimlendirmenin nasıl işlendiği konusunda önemli ölçüde farklılık gösterir.

Bir Razor sayfa veya görünüm işlendiğinde, her Razor kod satırı metın biçiminde HTML yayar. Oluşturulduktan sonra sunucu, üretilen herhangi bir durum da dahil olmak üzere sayfayı veya görünüm örneğini ortadan kaldırır. Sayfa için başka bir istek gerçekleştiğinde, örneğin sunucu doğrulaması başarısız olduğunda ve doğrulama özeti görüntülendiğinde:

* Sayfanın tamamı HTML metnine yeniden eklenir.
* Sayfa istemciye gönderilir.

BlazorUygulama, *bileşen*olarak adlandırılan Kullanıcı arabiriminin yeniden kullanılabilir öğelerinden oluşur. Bir bileşen C# kodu, biçimlendirme ve diğer bileşenleri içerir. Bir bileşen işlendiğinde, bir Blazor HTML veya XML belge nesne modeli (DOM) gibi dahil edilen bileşenlerin bir grafiğini üretir. Bu grafik, özelliklerde ve alanlarında tutulan bileşen durumunu içerir. Blazorbiçimlendirmenin ikili gösterimini üretmek için bileşen grafiğini değerlendirir. İkili biçimi şu şekilde olabilir:

* HTML metnine açıldı (prerendering sırasında &dagger; ).
* Düzenli işleme sırasında biçimlendirmeyi verimli bir şekilde güncelleştirmek için kullanılır.

&dagger;*Prerendering*: istenen Razor Bileşen, sunucuda statik HTML 'ye derlendi ve istemciye gönderildiğinde, kullanıcıya işlendiğinde. İstemci ve sunucu arasında bağlantı kurulduktan sonra, bileşenin statik ön işlenmiş öğeleri etkileşimli öğelerle değiştirilmiştir. Prerendering, uygulamanın kullanıcıya daha fazla yanıt vermesini sağlar.

İçindeki bir kullanıcı arabirimi güncelleştirmesi Blazor tarafından tetiklenir:

* Düğme seçme gibi kullanıcı etkileşimi.
* Zamanlayıcı gibi uygulama Tetikleyicileri.

Grafik yeniden tanımlanır ve bir UI *farkı* (fark) hesaplanır. Bu fark, istemcideki Kullanıcı arabirimini güncelleştirmek için gereken en küçük DOM düzenlemelerinin kümesidir. Fark istemciye bir ikili biçimde gönderilir ve tarayıcı tarafından uygulanır.

Kullanıcı, istemci üzerinde bundan uzaklaştığında bir bileşen atılmış olur. Bir Kullanıcı bir bileşenle etkileşim kurarken, bileşenin durumu (hizmetler, kaynaklar) sunucunun belleğinde tutulmalıdır. Birçok bileşenin durumu sunucu tarafından eşzamanlı olarak Korunabileceğinden, bellek tükenmesi sorunu ele alınmalıdır. Sunucu Blazor belleğinin en iyi şekilde kullanılmasını sağlamak üzere bir sunucu uygulaması yazmak hakkında yönergeler için, bkz <xref:blazor/security/server/threat-mitigation> ..

### <a name="circuits"></a>Uygulanıp

BlazorSunucu uygulaması [ASP.NET Core SignalR ](xref:signalr/introduction)üzerine kurulmuştur. Her istemci, devre adlı bir veya daha fazla bağlantı üzerinden sunucusuyla iletişim kurar SignalR . *circuit* Devre, Blazor SignalR geçici ağ kesintilerine tolerans sağlayan bağlantılar üzerinde soyutlamadır. Blazorİstemci SignalR bağlantının kesileceğini gördüğünde, yeni bir bağlantı kullanarak sunucuya yeniden bağlanmaya çalışır SignalR .

Bir sunucu uygulamasına bağlı her tarayıcı ekranı (tarayıcı sekmesi veya iframe) Blazor bir SignalR bağlantı kullanır. Bu, tipik sunucu tarafından işlenmiş uygulamalarla karşılaştırıldığında daha önemli bir ayırım ifade etmiştir. Sunucu tarafından işlenen bir uygulamada, aynı uygulamayı birden çok tarayıcı ekranında açmak genellikle sunucuda ek kaynak taleplerine çevirilmez. Bir Blazor sunucu uygulamasında, her tarayıcı ekranı, sunucu tarafından yönetilecek ayrı bir devre ve bileşen durumunun ayrı örneklerini gerektirir.

Blazortarayıcı sekmesini kapatmayı *veya bir dış* URL 'ye gidilmesini göz önünde bulundurur. Düzgün sonlandırma durumunda, devre ve ilişkili kaynaklar hemen serbest bırakılır. Bir istemci, örneğin bir ağ kesintisi nedeniyle düzgün şekilde kesilmeyen bir şekilde kesilebilir. BlazorSunucu, istemcinin yeniden bağlanmasına izin vermek için, yapılandırılabilir bir Aralık için bağlantısı kesilen devreleri depolar.

BlazorSunucu, kodun bir Kullanıcı devresi durumunda değişiklikler üzerinde kod çalıştırmaya izin veren bir *devhandler*tanımlamasına olanak tanır. Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>UI gecikmesi

UI gecikme süresi, başlatılan bir eylemden Kullanıcı arabiriminin güncelleştirildiği zamana kadar geçen süredir. Bir uygulamanın kullanıcıya yanıt vermesi için kullanıcı ARABIRIMI gecikmesi için daha küçük değerler zorunludur. Bir Blazor sunucu uygulamasında her bir eylem sunucusuna gönderilir, işlenir ve bır UI farkı geri gönderilir. Sonuç olarak, UI gecikmesi ağ gecikme süresinin toplamı ve eylemi işlerken sunucu gecikmesi sayısıdır.

Özel bir kurumsal ağla sınırlı bir iş kolu uygulaması için, ağ gecikmesi nedeniyle kullanıcı gecikmesi algılarını üzerindeki etki, genellikle çok sayıda CEPSİZ olur. Internet üzerinden dağıtılan bir uygulama için, özellikle de kullanıcılar coğrafi olarak coğrafi olarak dağıtılmışsa gecikme süresi kullanıcılara karşı farklılık gösterebilir.

Bellek kullanımı ayrıca uygulama gecikme süresine de katkıda bulunabilir. Daha fazla bellek kullanımı, her ikisi de uygulama performansının düşmesine neden olan ve bu nedenle kullanıcı arabirimi gecikmesini arttığı diskte sık görülen çöp toplama veya disk belleği belleği

BlazorSunucu uygulamaları, ağ gecikmesini ve bellek kullanımını azaltarak UI gecikmesini en aza indirmek için iyileştirilmelidir. Ağ gecikmesini ölçmeye yönelik bir yaklaşım için bkz <xref:blazor/host-and-deploy/server#measure-network-latency> .. Ve hakkında daha fazla bilgi için SignalR Blazor bkz.

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Sunucuyla bağlantı

BlazorSunucu uygulamaları sunucuya etkin bir SignalR bağlantı gerektirir. Bağlantı kaybolursa, uygulama sunucuya yeniden bağlanmaya çalışır. İstemcinin durumu hala bellekte olduğu sürece, istemci oturumu durum kaybı olmadan devam eder.

Sunucu Blazor uygulaması, sunucu üzerinde kullanıcı arabirimi durumunu ayarlayan ilk istemci isteğine yanıt olarak ön ekler. İstemci bir bağlantı oluşturmayı denediğinde SignalR , istemci aynı sunucuya yeniden bağlanmalıdır. BlazorBirden fazla arka uç sunucusu kullanan sunucu uygulamaları, bağlantılar için *yapışkan oturumlar* uygulamalıdır SignalR .

Sunucu uygulamaları için [Azure SignalR hizmetini](/azure/azure-signalr) kullanmanızı öneririz Blazor . Hizmet, bir Blazor sunucu uygulamasının ölçeğini çok sayıda eşzamanlı bağlantıya ölçeklendirmeye olanak tanır SignalR . Sticky Sessions, SignalR hizmetin `ServerStickyMode` seçenek veya yapılandırma değeri olarak ayarlanarak Azure hizmeti için etkinleştirilir `Required` . Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/server#signalr-configuration>.

IIS kullanırken, yapışkan oturumlar uygulama Isteği yönlendirme ile etkinleştirilir. Daha fazla bilgi için bkz. [uygulama Isteği yönlendirme kullanarak HTTP yük dengelemesi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
