---
title: ASP.NET Blazor Core hosting modelleri
author: guardrex
description: WebAssembly Blazor ve Blazor Sunucu barındırma modellerini anlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471833"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.NET Blazor Core hosting modelleri

Yazar: [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor[Bir WebAssembly](https://webassembly.org/)tabanlı .NET runtime* Blazor (WebAssembly)* veya sunucu tarafında ASP.NET Core* Blazor (Sunucu)* üzerinde tarayıcıda istemci tarafı çalıştırmak için tasarlanmış bir web çerçevesidir. Barındırma modeli ne olursa olsun, uygulama ve bileşen modelleri *aynıdır.*

Bu makalede açıklanan barındırma modelleri için bir <xref:blazor/get-started>proje oluşturmak için bkz.

Gelişmiş yapılandırma için <xref:blazor/hosting-model-configuration>bkz.

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly

Ana barındırma modeli, Blazor WebAssembly'deki tarayıcıda istemci tarafında çalışıyor. Uygulama, Blazor bağımlılıkları ve .NET çalışma süresi tarayıcıya indirilir. Uygulama doğrudan tarayıcı UI iş parçacığı üzerinde yürütülür. UI güncelleştirmeleri ve olay işleme aynı işlem içinde oluşur. Uygulamanın varlıkları, istemcilere statik içerik sunabilecek bir web sunucusuna veya hizmetine statik dosya olarak dağıtılır.

![BlazorWebAssembly: Blazor Uygulama tarayıcı içinde bir UI iş parçacığı üzerinde çalışır.](hosting-models/_static/blazor-webassembly.png)

İstemci Blazor tarafı barındırma modelini kullanarak bir uygulama oluşturmak için ** Blazor WebAssembly App** şablonu[(dotnet yeni blazorwasm)](/dotnet/core/tools/dotnet-new)kullanın.

** Blazor WebAssembly App** şablonu seçtikten sonra, **ASP.NET Core barındırılan** onay kutusunu[(dotnet yeni blazorwasm --barındırılan)](/dotnet/core/tools/dotnet-new)seçerek uygulamayı ASP.NET Core arka uç kullanacak şekilde yapılandırma seçeneğiniz vardır. ASP.NET Core Blazor uygulaması, uygulamayı istemcilere hizmet eder. WebAssembly uygulaması, web API çağrılarını kullanarak ağ [SignalR](xref:signalr/introduction) üzerinden sunucuyla etkileşimkurabilir veya ( ).<xref:tutorials/signalr-blazor-webassembly> Blazor

Şablonlar, işleyen `blazor.webassembly.js` komut dosyasını içerir:

* .NET çalışma saatini, uygulamayı ve uygulamanın bağımlılıklarını indirme.
* Uygulamayı çalıştırmak için çalışma zamanının başlatılması.

Blazor WebAssembly barındırma modeli çeşitli avantajlar sunar:

* .NET sunucu tarafı bağımlılığı yoktur. Uygulama istemciye indirildikten sonra tam olarak çalışır.
* İstemci kaynakları ve yetenekleri tamamen kaldıraçlı.
* Çalışma sunucudan istemciye boşaltılır.
* Uygulamayı barındırmak için ASP.NET Core web sunucusu gerekmez. Sunucusuz dağıtım senaryoları mümkündür (örneğin, uygulamaya bir CDN'den hizmet vermektedir).

WebAssembly barındırma Blazor dezavantajları vardır:

* Uygulama tarayıcının özellikleriyle sınırlıdır.
* Yetenekli istemci donanımı ve yazılımı (örneğin, WebAssembly desteği) gereklidir.
* İndirme boyutu daha büyüktür ve uygulamaların yüklenmesi daha uzun sürer.
* .NET çalışma süresi ve takım desteği daha az olgundur. Örneğin, [.NET Standart](/dotnet/standard/net-standard) desteği nde ve hata ayıklamada sınırlamalar vardır.

Blazor Barındırılan uygulama modeli [Docker kapsayıcılarını](/dotnet/standard/microservices-architecture/container-docker-introduction/index)destekler. Visual Studio'daki Sunucu projesine sağ tıklayın ve**Docker Desteği** **Ekle'yi** > seçin.

## <a name="opno-locblazor-server"></a>BlazorSunucu

Blazor Sunucu barındırma modeli ile uygulama, ASP.NET Core uygulaması nın içinden sunucuda yürütülür. UI güncelleştirmeleri, olay işleme ve JavaScript [SignalR](xref:signalr/introduction) çağrıları bir bağlantı üzerinden işlenir.

![Tarayıcı, SignalR bir bağlantı üzerinden sunucudaki uygulamayla (ASP.NET Core uygulamasının içinde barındırılan) ile etkileşime girebeder.](hosting-models/_static/blazor-server.png)

Sunucu barındırma Blazor modelini Blazor kullanarak bir uygulama oluşturmak için, ASP.NET Core ** Blazor Server App** şablonu[(dotnet yeni blazorserver)](/dotnet/core/tools/dotnet-new)kullanın. ASP.NET Core uygulaması Blazor Sunucu uygulamasına ev SignalR sahipliği yapıyor ve istemcilerin bağlandığı bitiş noktasını oluşturuyor.

ASP.NET Core uygulaması, uygulamanın `Startup` sınıfına aşağıdakileri eklemek için başvurur:

* Sunucu tarafı hizmetleri.
* İstek işleme boru hattı için uygulama.

`blazor.server.js` Komut dosyası istemci bağlantısını kurar. Uygulama durumunu gerektiği gibi sürdürmek ve geri yüklemek uygulamanın sorumluluğudur (örneğin, ağ bağlantısının kaybolması durumunda). `blazor.server.js` Komut dosyası, ASP.NET Core paylaşılan çerçevesinde gömülü bir kaynaktan sunulur.

Blazor Sunucu barındırma modeli çeşitli avantajlar sunar:

* İndirme boyutu bir Blazor WebAssembly uygulamasından önemli ölçüde daha küçüktür ve uygulama çok daha hızlı yüklenir.
* Uygulama, .NET Core uyumlu API'lerin kullanımı da dahil olmak üzere sunucu özelliklerinden tam olarak yararlanır.
* Sunucudaki .NET Core uygulamayı çalıştırmak için kullanılır, bu nedenle hata ayıklama gibi varolan .NET araç kullanımı beklendiği gibi çalışır.
* İnce istemciler desteklenir. Örneğin, Blazor Sunucu uygulamaları WebAssembly'i desteklemeyen tarayıcılarla ve kaynak kısıtlamalı aygıtlarla çalışır.
* Uygulamanın bileşen kodu da dahil olmak üzere .NET/C# kod tabanı istemcilere sunulmaz.

Sunucu barındırma dezavantajları Blazor vardır:

* Yüksek gecikme genellikle vardır. Her kullanıcı etkileşimi bir ağ atlama içerir.
* Çevrimdışı destek yok. İstemci bağlantısı başarısız olursa, uygulama çalışmayı durdurur.
* Ölçeklenebilirlik, birçok kullanıcısı olan uygulamalar için zordur. Sunucu, birden çok istemci bağlantısını yönetmeli ve istemci durumunu işlemelidir.
* Uygulamaya hizmet vermek için ASP.NET Core sunucusu gereklidir. Sunucusuz dağıtım senaryoları mümkün değildir (örneğin, uygulamaya bir CDN'den hizmet vermektedir).

Sunucu uygulama modeli [Docker kapsayıcılarını](/dotnet/standard/microservices-architecture/container-docker-introduction/index)destekler. Blazor Visual Studio'da projeye sağ tıklayın ve**Docker Desteği** **Ekle'yi** > seçin.

### <a name="comparison-to-server-rendered-ui"></a>Sunucu tarafından işlenen ui ile karşılaştırma

Sunucu uygulamalarını Blazor anlamanın bir yolu, Razor görünümleri veya Razor Pages kullanarak ASP.NET Core uygulamalarında uI oluşturmanın geleneksel modellerinden nasıl farklı olduğunu anlamaktır. Her iki model de HTML içeriğini açıklamak için Razor dilini kullanır, ancak biçimlendirmenin nasıl işlendiği konusunda önemli farklılıklar gösterir.

Bir Razor Page veya görünüm işlendiğinde, Razor kodunun her satırı html'i metin biçiminde yadar. İşlemden sonra, sunucu, üretilen herhangi bir durum da dahil olmak üzere sayfayı veya görüntü örneğini ortadan kakırır. Sayfa için başka bir istek oluştuğunda, örneğin sunucu doğrulama başarısız olduğunda ve doğrulama özeti görüntülendiğinde:

* Sayfanın tamamı yeniden HTML metnine işlenir.
* Sayfa istemciye gönderilir.

Bir Blazor uygulama, *ui*bileşenleri olarak adlandırılan yeniden kullanılabilir unsurlarından oluşur. Bileşen C# kodu, biçimlendirme ve diğer bileşenleri içerir. Bir bileşen işlendiğinde, Blazor html veya XML Document Object Model (DOM) benzer dahil bileşenlerin bir grafik üretir. Bu grafik, özellikler de ve alanlarda tutulan bileşen durumunu içerir. Blazorbiçimlendirmenin ikili bir gösterimini oluşturmak için bileşen grafiğini değerlendirir. İkili biçim şu olabilir:

* HTML metnine dönüştürüldü (ön işleme&dagger;sırasında).
* Düzenli görüntüleme sırasında biçimlendirmeyi verimli bir şekilde güncelleştirmek için kullanılır.

&dagger;İstenen Razor *bileşeninin önceden işlenmesi* &ndash; sunucuda statik HTML'de derlenir ve istemciye gönderilir ve kullanıcıya işlenir. İstemci ve sunucu arasında bağlantı yapıldıktan sonra, bileşenin statik önceden işlenmiş öğeleri etkileşimli öğelerle değiştirilir. Ön oluşturma, uygulamanın kullanıcıya karşı daha duyarlı hissetmesini sağlar.

Bir Kullanıcı Birkullanıcı Birkullanıcı Durumu güncelleştirmesi Blazor tarafından tetiklenir:

* Bir düğme seçmek gibi kullanıcı etkileşimi.
* Zamanlayıcı gibi uygulama tetikleyicileri.

Grafik yeniden işlenir ve bir UI *diff* (fark) hesaplanır. Bu diff, istemcide Kullanıcı Bira'sını güncelleştirmek için gereken en küçük DOM güncelleştirme kümesidir. Diff istemciye ikili biçimde gönderilir ve tarayıcı tarafından uygulanır.

Kullanıcı istemcide ondan uzaklaştıktan sonra bir bileşen atılır. Bir kullanıcı bir bileşenle etkileşimde iken, bileşenin durumu (hizmetler, kaynaklar) sunucunun belleğinde tutulmalıdır. Birçok bileşenin durumu sunucu tarafından aynı anda tutulabileceğinden, bellek tükenmesi giderilmesi gereken bir sorundur. Sunucu belleği kullanımını Blazor en iyi şekilde sağlamak için bir <xref:security/blazor/server>Server uygulamasının nasıl yazılakullanılacağı nailişkin rehberlik için bkz.

### <a name="circuits"></a>Devre

Bir Blazor Server uygulaması ASP.NET [Core SignalR ](xref:signalr/introduction)üstüne inşa edilmiştir. Her istemci *devre*adı verilen bir SignalR veya daha fazla bağlantı üzerinden sunucuya iletişim kurar. Bir devre, geçici ağ SignalR kesintilerine tahammül edebilen bağlantılar üzerindeki soyutlamadır. Blazor İstemci Blazor bağlantının SignalR kesildiğini gördüğünde, yeni SignalR bir bağlantı kullanarak sunucuya yeniden bağlanmaya çalışır.

Sunucu Blazor uygulamasına bağlı her tarayıcı ekranı (tarayıcı sekmesi veya SignalR iframe) bir bağlantı kullanır. Bu, tipik sunucu tarafından işlenen uygulamalarla karşılaştırıldığında önemli bir ayrımdır. Sunucu tarafından işlenen bir uygulamada, aynı uygulamayı birden çok tarayıcı ekranında açmak genellikle sunucudaki ek kaynak taleplerine dönüşmez. Sunucu Blazor uygulamasında, her tarayıcı ekranı sunucu tarafından yönetilecek ayrı bir devre ve ayrı bileşen durumu örnekleri gerektirir.

Blazorbir tarayıcı sekmesini kapatmayı veya harici bir URL'de gezinmeyi *zarif* bir sonlandırma olarak kabul eder. Zarif bir sonlandırma durumunda, devre ve ilişkili kaynaklar hemen serbest bırakılır. İstemci, örneğin bir ağ kesintisi nedeniyle, incelikle bağlantısını da kesebilir. BlazorSunucu, istemcinin yeniden bağlanmasına izin vermek için bağlantısı kesilen devreleri yapılandırılabilir bir aralık için depolar.

BlazorSunucu, kodun, kullanıcının devresinin durumuna ilişkin değişikliklerin kod çalışmasını sağlayan bir *devre işleyicisi*tanımlamasına izin verir. Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>UI Gecikme

UI gecikmesüresi, başlatılan bir eylemden UI'nin güncelleştirilen zamana kadar geçen süredir. Kullanıcı ara birimi için daha küçük değerler, bir uygulamanın kullanıcıya yanıt vermesini hissetmesi için zorunludur. Sunucu Blazor uygulamasında, her eylem sunucuya gönderilir, işlenir ve bir UI diff geri gönderilir. Sonuç olarak, UI gecikmesi, ağ gecikmesinin ve sunucu gecikmesinin toplamıdır.

Özel bir şirket ağıyla sınırlı bir iş uygulaması için, ağ gecikmesi nedeniyle kullanıcı gecikmesi algıları üzerindeki etkisi genellikle algılanamaz. Internet üzerinden dağıtılan bir uygulama için gecikme, özellikle kullanıcılar coğrafi olarak yaygın olarak dağıtılıyorsa, kullanıcılar tarafından fark edilebilir hale gelebilir.

Bellek kullanımı da uygulama gecikmesine katkıda bulunabilir. Artan bellek kullanımı, sık sık çöp toplama veya bellek diske sayfalama ile sonuçlanır ve bu da uygulama performansını düşürür ve dolayısıyla kullanıcı aramasını artırır. Daha fazla bilgi için bkz. <xref:security/blazor/server>.

BlazorSunucu uygulamaları, ağ gecikmesini ve bellek kullanımını azaltarak Kullanıcı Arası gecikmesini en aza indirmek için optimize edilmelidir. Ağ gecikmesini ölçmeyaklaşımı için bkz. <xref:host-and-deploy/blazor/server#measure-network-latency> Daha fazla SignalR bilgi Blazoriçin ve , bkz:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Sunucuya bağlantı

BlazorSunucu uygulamaları sunucuya etkin SignalR bir bağlantı gerektirir. Bağlantı kaybolursa, uygulama sunucuya yeniden bağlanmaya çalışır. İstemcinin durumu hala bellekte olduğu sürece, istemci oturumu durum kaybetmeden devam eder.

Sunucuda UI durumunu ayarlayan ilk istemci isteğine yanıt olarak bir Blazor Sunucu uygulaması önişler. İstemci bir SignalR bağlantı oluşturmaya çalıştığında, istemcinin aynı sunucuya yeniden bağlanması gerekir. BlazorBirden fazla arka uç sunucusu kullanan sunucu uygulamaları, SignalR bağlantılar için *yapışkan oturumlar* uygulamalıdır.

Sunucu uygulamaları için Blazor [ SignalR Azure Hizmetini](/azure/azure-signalr) kullanmanızı öneririz. Bu hizmet, bir Blazor Server uygulamasını çok sayıda eşzamanlı SignalR bağlantıya ölçeklemenize olanak tanır. Azure SignalR Hizmeti için yapışkan oturumlar, hizmetin `ServerStickyMode` seçeneğini veya yapılandırma `Required`değerini 'ye ayarlayarak etkinleştirilir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/server#signalr-configuration>.

IIS kullanırken, Uygulama İsteği Yönlendirmesi ile yapışkan oturumlar etkinleştirilir. Daha fazla bilgi [için, Uygulama İsteği Yönlendirme'yi kullanarak HTTP Yük Dengeleme'ye](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
