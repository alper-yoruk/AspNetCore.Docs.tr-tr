---
title: ASP.NET Core weelsembly ile aşamalı Web uygulamaları oluşturun Blazor
author: guardrex
description: BlazorMasaüstü uygulaması gibi davranması için modern tarayıcı özelliklerini kullanan bir tabanlı aşamalı Web uygulaması (PWA) oluşturmayı öğrenin.
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 274516014c027972166402abc70d22fa801898de
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451855"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>ASP.NET Core weelsembly ile aşamalı Web uygulamaları oluşturun Blazor

[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Aşamalı bir Web uygulaması (PWA), masaüstü uygulaması gibi davranması için modern tarayıcı API 'Leri ve özellikleri kullanan tek sayfalı bir uygulamadır (SPA). BlazorWebAssembly, standartlara dayalı bir istemci tarafı Web uygulaması platformudur, bu nedenle aşağıdaki yetenekler için gereken PWA API 'Leri de dahil olmak üzere herhangi bir tarayıcı API 'sini kullanabilir:

* Ağ hızından bağımsız olarak çevrimdışı çalışma ve anında yükleme.
* Yalnızca bir tarayıcı penceresi değil kendi uygulama penceresinde çalışıyor.
* Konağın işletim sistemi Başlat menüsünden, Dock veya Ana ekranınızdan başlatılmakta.
* Kullanıcı uygulamayı kullanmıyor olsa bile, arka uç sunucusundan anında iletme bildirimleri alma.
* Arka planda otomatik olarak güncelleştiriliyor.

*İlerleyen* sözcük, bu tür uygulamaları tanımlamakta kullanılır çünkü:

* Bir Kullanıcı öncelikle uygulamayı diğer SPA gibi Web tarayıcıları içinde bulabilir ve kullanabilir.
* Daha sonra Kullanıcı, bu uygulamayı işletim sistemine yüklemeyi ve anında iletme bildirimlerini etkinleştirmeyi ilerler.

## <a name="create-a-project-from-the-pwa-template"></a>PWA şablonundan proje oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni bir **proje oluştur** iletişim kutusunda yeni bir ** Blazor Webassembly uygulaması** oluştururken, **aşamalı Web uygulaması** onay kutusunu seçin:

![Visual Studio yeni proje iletişim kutusunda ' aşamalı Web uygulaması ' onay kutusu seçilidir.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Anahtarla bir komut kabuğunda bir PWA projesi oluşturun `--pwa` :

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

İsteğe bağlı olarak, ASP.NET Core barındırılan şablondan oluşturulan bir uygulama için PWA yapılandırılabilir. PWA senaryosu barındırma modelinden bağımsızdır.

## <a name="installation-and-app-manifest"></a>Yükleme ve uygulama bildirimi

PWA şablonu kullanılarak oluşturulan bir uygulamayı ziyaret ederken, kullanıcılar uygulamayı işletim sistemi başlangıç menüsüne, Dock 'a veya ana ekrana yükleme seçeneğine sahiptir. Bu seçeneğin sunulma şekli kullanıcının tarayıcısına bağlıdır. Edge veya Chrome gibi Desktop Kmıum tabanlı tarayıcıları kullanılırken, URL çubuğu içinde bir **Ekle** düğmesi görünür. Kullanıcı **Ekle** düğmesini seçtikten sonra bir onay iletişim kutusu alırlar:

![Google Chrome 'daki onaylama diaglog, kullanıcıya ' MyBlazorPwa ' uygulaması için bir Install Button düğmesi gösterir.](progressive-web-app/_static/image2.png)

İOS 'ta, ziyaretçiler Safari 'nin **Share** düğmesini ve **Add to HOMESCREEN** seçeneğini kullanarak PWA 'yı yükleyebilir. Android için Chrome 'da kullanıcılar, sağ üst köşedeki **menü** düğmesini ve ardından **Giriş ekranına Ekle**' yi seçer.

Yüklendikten sonra uygulama, adres çubuğu olmadan kendi penceresinde görünür:

![' MyBlazorPwa ' uygulaması, bir adres çubuğu olmadan Google Chrome 'da çalışır.](progressive-web-app/_static/image3.png)

Pencerenin başlığını, renk şemasını, simgesini veya diğer ayrıntıları özelleştirmek için projenin *Wwwroot* dizinindeki *manifest. JSON* dosyasına bakın. Bu dosyanın şeması Web standartları tarafından tanımlanır. Daha fazla bilgi için bkz. [MDN Web belgeleri: Web uygulaması bildirimi](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Çevrimdışı destek

Varsayılan olarak, PWA şablonu seçeneği kullanılarak oluşturulan uygulamalar, çevrimdışı çalışmaya yönelik desteğe sahiptir. Bir kullanıcının çevrimiçi olduklarında uygulamayı ilk kez ziyaret etmelidir. Tarayıcı, çevrimdışı çalışmak için gereken tüm kaynakları otomatik olarak indirir ve önbelleğe alır.

> [!IMPORTANT]
> Geliştirme desteği, değişiklik yapma ve test etme işlemleri için olağan geliştirme döngüsünü kesintiye uğratıyor. Bu nedenle, çevrimdışı destek yalnızca *yayımlanan* uygulamalar için etkinleştirilmiştir. 

> [!WARNING]
> Çevrimdışı etkin bir PWA dağıtmak istiyorsanız, [bazı önemli uyarılar ve uyarılar](#caveats-for-offline-pwas)vardır. Bu senaryolar, ' ye özgü olmayan çevrimdışı PWAs 'a ait değildir Blazor . Çevrimdışı etkin uygulamanızın nasıl çalıştığı hakkında varsayımlar yapmadan önce bu uyarıları okuduğunuzdan ve anladığınızdan emin olun.

Çevrimdışı desteğin nasıl çalıştığını görmek için:

1. Uygulamayı yayımlayın. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Uygulamayı HTTPS 'yi destekleyen bir sunucuya dağıtın ve uygulamayı güvenli HTTPS adresinde bir tarayıcıda erişin.
1. Tarayıcının geliştirme araçlarını açın ve **uygulama** sekmesinde bir *hizmet çalışanının* konak için kayıtlı olduğunu doğrulayın:

   ![Google Chrome Geliştirici Araçları ' uygulama ' sekmesi, etkinleştirilen ve çalışan bir hizmet çalışanını gösterir.](progressive-web-app/_static/image4.png)

1. Sayfayı yeniden yükleyin ve **ağ** sekmesini Inceleyin. **hizmet çalışanı** veya **bellek önbelleği** , tüm sayfa varlıklarının kaynakları olarak listelenir:

   ![Tüm sayfa varlıklarının kaynaklarını gösteren Google Chrome Geliştirici Araçları ' ağ ' sekmesi.](progressive-web-app/_static/image5.png)

1. Tarayıcının, uygulamayı yüklemek için ağ erişimi 'ne bağımlı olmadığını doğrulamak için şunlardan birini yapın:

   * Web sunucusunu kapatın ve sayfa yeniden yükler dahil uygulamanın normal şekilde çalışmaya devam ettiğini görün. Benzer şekilde, yavaş bir ağ bağlantısı olduğunda uygulama normal şekilde çalışmaya devam eder.
   * **Ağ** sekmesinde çevrimdışı modunun benzetimini yapmak için tarayıcıya bildirin:

   ![Tarayıcı modu açılan, ' çevrimiçi ' iken ' Çevrimdışı ' olarak değiştirilen Google Chrome Geliştirici Araçları ' Ağı ' sekmesi.](progressive-web-app/_static/image6.png)

Hizmet çalışanı kullanan çevrimdışı destek, için özel olmayan bir web standardıdır Blazor . Hizmet çalışanları hakkında daha fazla bilgi için bkz. [MDN Web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Hizmet çalışanları için ortak kullanım desenleri hakkında daha fazla bilgi için bkz. [Google Web: hizmet çalışanı yaşam döngüsü](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Blazoröğesinin PWA şablonu iki hizmet çalışanı dosyası üretir:

* geliştirme sırasında kullanılan *Wwwroot/Service-Worker. js*.
* Uygulama yayımlandıktan sonra kullanılan *Wwwroot/Service-Worker. yayınlanan. js*.

İki hizmet çalışanı dosyası arasındaki mantığı paylaşmak için aşağıdaki yaklaşımı göz önünde bulundurun:

* Ortak mantığı barındırmak için üçüncü bir JavaScript dosyası ekleyin.
* [Self. ımportscripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) kullanarak ortak mantığı her iki hizmet çalışanı dosyasına da yükleyin.

### <a name="cache-first-fetch-strategy"></a>Cache-ilk getirme stratejisi

Yerleşik *Service-Worker. yayımlanmış. js* hizmet çalışanı, istekleri bir *Cache-First* stratejisi kullanarak çözer. Bu, kullanıcının ağ erişimi mi yoksa daha yeni içerik mi olduğunu bağımsız olarak, hizmet çalışanının önbelleğe alınmış içeriği döndürmeyi tercih ettiği anlamına gelir.

Önbellek-ilk strateji, şu nedenle değerlidir:

* **Güvenilirlik sağlar.** Ağ erişimi Boolean bir durum değil. Kullanıcı yalnızca çevrimiçi veya çevrimdışı değildir:

  * Kullanıcının cihazı çevrimiçi olduğunu varsayabilir, ancak ağ bu kadar yavaş olabilir.
  * Ağ, bazı istekleri engelleyen veya yeniden yönlendirmekte olan bir açıklamalı WIFI portalı olduğunda olduğu gibi belirli URL 'Ler için geçersiz sonuçlar döndürebilir.
  
  Bunun nedeni tarayıcının `navigator.onLine` API 'sinin güvenilir olmaması ve bağımlı olmaması gerekir.

* **Doğruluk sağlar.** Bir çevrimdışı kaynak önbelleği oluştururken hizmet çalışanı, kaynakların tek bir anında tam ve otomatik tutarlı bir anlık görüntüsünü döndürüldüğünü güvence altına almak için içerik karmaını kullanır. Bu önbellek daha sonra atomik birim olarak kullanılır. Yalnızca daha önce önbelleğe alınmış olan sürümler olduğundan, ağı yeni kaynaklar için soran bir nokta yoktur. Diğer risk tutarsızlığı ve uyumsuzluk (örneğin, birlikte derlenmeyen .NET derlemelerinin sürümlerini kullanmaya çalışmak).

### <a name="background-updates"></a>Arka plan güncelleştirmeleri

Bir akıl modeli olarak, yüklenebilen bir mobil uygulama gibi davranan bir çevrimdışı ilk PWA 'yı düşünebilirsiniz. Uygulama, ağ bağlantısından bağımsız olarak hemen başlatılır, ancak yüklü uygulama mantığı, en son sürüm olmayan bir noktadan itibaren anlık görüntüden gelir.

BlazorPWA şablonu, Kullanıcı her ziyaret ettiğinde ve çalışan bir ağ bağlantısı olduğunda kendiliğinden otomatik olarak güncelleştirmeyi deneyen uygulamalar üretir. Bu şekilde çalışma şekli şöyledir:

* Derleme sırasında, proje bir *hizmet çalışanı varlık bildirimi*oluşturur. Varsayılan olarak bu, *Service-Worker-assets. js*olarak adlandırılır. Bildirim, uygulamanın, içerik karmaları dahil .NET derlemeleri, JavaScript dosyaları ve CSS gibi çevrimdışı çalışması için gereken tüm statik kaynakları listeler. Kaynak listesi, hangi kaynakların önbellekte olduğunu bilmesi için hizmet çalışanı tarafından yüklenir.
* Kullanıcı uygulamayı her ziyaret ettiğinde, tarayıcı arka planda *Service-Worker. js* ve *Service-Worker-assets. js* ' yi yeniden ister. Dosyalar, mevcut yüklü hizmet çalışanı ile bayt için bayt olarak karşılaştırılır. Sunucu, bu dosyalardan herhangi biri için değiştirilen içerik döndürürse, hizmet çalışanı kendi yeni bir sürümünü yüklemeye çalışır.
* Yeni bir sürümü yüklenirken, hizmet çalışanı çevrimdışı kaynaklar için yeni, ayrı bir önbellek oluşturur ve önbelleği *Service-Worker-assets. js*' de listelenen kaynaklarla doldurmaya başlar. Bu mantık, `onInstall` *Service-Worker. yayınlanmış. js*içindeki işlevde uygulanır.
* Tüm kaynaklar hatasız olarak yüklendiğinde ve tüm içerik karmalarının eşleşmesi durumunda işlem başarıyla tamamlanır. Başarılı olursa, yeni hizmet çalışanı *etkinleştirme durumunu bekliyor* olarak girer. Kullanıcı uygulamayı kapatır (uygulama sekmeleri veya pencereler olmadan), yeni hizmet çalışanı *etkin* hale gelir ve sonraki uygulama ziyaretleri için kullanılır. Eski hizmet çalışanı ve önbelleği silinir.
* İşlem başarıyla tamamlanmazsa, yeni hizmet çalışanı örneği atılır. İstemci, istekleri tamamlayabildikleri daha iyi bir ağ bağlantısına sahip olduğunda, bu güncelleştirme işlemi kullanıcının sonraki ziyaretinin üzerinde yeniden denenir.

Bu işlemi, hizmet çalışan mantığını düzenleyerek özelleştirin. Önceki davranışın hiçbiri öğesine özgü değildir Blazor ancak yalnızca PWA şablonu seçeneği tarafından belirtilen varsayılan deneyimdir. Daha fazla bilgi için bkz. [MDN Web belgeleri: hizmet çalışanı API 'si](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>İsteklerin çözümlenmesi

[Önbellek-ilk getirme stratejisi](#cache-first-fetch-strategy) bölümünde açıklandığı gibi, varsayılan hizmet çalışanı bir *ön uç* stratejisi kullanır, yani kullanılabilir olduğunda önbelleğe alınmış içeriklere hizmet vermeye çalışır. Belirli bir URL için önbelleğe alınmış içerik yoksa (örneğin, arka uç API 'sinden veri istenirken), hizmet çalışanı normal bir ağ isteğine geri döner. Sunucu ulaşılabilir olduğunda ağ isteği başarılı olur. Bu mantık, `onFetch` *Service-Worker. yayınlanmış. js*içinde işlevin içinde uygulanır.

Uygulamanın Razor bileşenleri, arka uç API 'lerinden veri istemeyi ve ağ kullanım dışı nedenlerle başarısız istekler için kolay bir kullanıcı deneyimi sağlamak istiyorsanız, uygulamanın bileşenleri içinde Logic uygulayın. Örneğin, `try/catch` istekleri etrafında kullanın <xref:System.Net.Http.HttpClient> .

### <a name="support-server-rendered-pages"></a>Sunucu tarafından işlenen sayfaları destekleme

Kullanıcı, veya gibi bir URL 'ye ilk kez gittiğinde ne olacağını göz önünde bulundurun `/counter` . Bu gibi durumlarda, olarak önbelleğe alınmış içeriği döndürmek istemezsiniz `/counter` , bunun yerine, `/index.html` ısbassembly uygulamanızı başlatmak için tarayıcıya önbelleğe alınmış içeriği yüklemesi gerekir Blazor . Bu ilk istekler, şu şekilde *Gezinti* istekleri olarak bilinir:

* görüntüler, stil sayfaları veya diğer dosyalar için *alt kaynak* istekleri.
* API verileri için *Fetch/XHR* istekleri.

Varsayılan hizmet çalışanı, gezinti istekleri için özel durum mantığı içerir. Hizmet çalışanı, istenen URL 'den bağımsız olarak, için önbelleğe alınmış içeriği döndürerek istekleri çözer `/index.html` . Bu mantık, `onFetch` *Service-Worker. yayınlanmış. js*içindeki işlevde uygulanır.

Uygulamanızda sunucu tarafından işlenmiş HTML döndürmesi gereken belirli URL 'Ler varsa ve önbellekten hizmet vermezse `/index.html` , hizmet çalışanınızdaki mantığı düzenlemeniz gerekir. İçeren tüm URL 'Lerin `/Identity/` sunucuya düzenli olarak yalnızca çevrimiçi istekleri olarak işlenmesi gerekiyorsa, *Service-Worker. yayınlanmış. js* `onFetch` mantığını değiştirin. Aşağıdaki kodu bulun:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Kodu aşağıdaki şekilde değiştirin:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Bunu yapmazsanız, ağ bağlantısından bağımsız olarak, hizmet çalışanı bu tür URL 'Ler için istekleri karşılar ve bunları kullanarak çözer `/index.html` .

### <a name="control-asset-caching"></a>Varlık önbelleğe alma denetimi

Projeniz MSBuild özelliğini tanımlarsa `ServiceWorkerAssetsManifest` , Blazor derleme araçları, belirtilen ada sahip bir hizmet çalışanı varlık bildirimi oluşturur. Varsayılan PWA şablonu, aşağıdaki özelliği içeren bir proje dosyası üretir:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Dosya *Wwwroot* çıkış dizinine yerleştirilir, bu nedenle tarayıcı bu dosyayı isteyerek alabilir `/service-worker-assets.js` . Bu dosyanın içeriğini görmek için bir metin düzenleyicisinde */BIN/Debug/{Target Framework}/Wwwroot/Service-Worker-assets.js* dosyasını açın. Ancak, her bir derlemede yeniden oluşturulduğundan dosyayı düzenlemeyin.

Varsayılan olarak, bu bildirim şunları listeler:

* Blazor.NET derlemeleri ve .net WebAssembly çalışma zamanı dosyaları gibi, çevrimdışı çalışmak için gerekli olan herhangi bir yönetilen kaynak.
* Dış projeler ve NuGet paketleri tarafından sağlanan statik Web varlıkları dahil olmak üzere, uygulamanın *Wwwroot* dizinine yayımlama için tüm kaynaklar (örneğin, resimler, stil sayfaları ve JavaScript dosyaları).

`onInstall` *Service-Worker. yayımlandı. js*' de içindeki mantığı düzenleyerek, bu kaynakların hangilerinin hizmet çalışanı tarafından alınıp önbelleğe alınacağını denetleyebilirsiniz. Varsayılan olarak, hizmet çalışanı *. html*, *. css*, *. js*ve *.* TDA gibi tipik Web dosya adı uzantılarına Blazor (.*DLL*, *. pdb*) özgü dosya türleri ile eşleşen dosyaları getirir ve önbelleğe alır.

Uygulamanın *Wwwroot* dizininde bulunmayan ek kaynakları dahil etmek için, `ItemGroup` Aşağıdaki örnekte gösterildiği gibi ek MSBuild girişleri tanımlayın:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl`Meta veriler, tarayıcının önbelleğe kaynağı getirilirken kullanması gereken temel göreli URL 'yi belirtir. Bu, diskteki özgün kaynak dosya adından bağımsız olabilir.

> [!IMPORTANT]
> Eklemek `ServiceWorkerAssetsManifestItem` , dosyanın uygulamanın *Wwwroot* dizininde yayımlanmasına neden olmaz. Yayımlama çıkışı ayrı olarak denetlenmelidir. `ServiceWorkerAssetsManifestItem`Yalnızca hizmet çalışanı varlıkları bildiriminde ek bir girdinin görünmesine neden olur.

## <a name="push-notifications"></a>Anında iletme bildirimleri

Diğer herhangi bir PWA gibi, Blazor webassembly PWA, arka uç sunucusundan anında iletme bildirimleri alabilir. Sunucu, uygulamayı etkin bir şekilde kullanmıyor olsa bile, herhangi bir zamanda anında iletme bildirimleri gönderebilir. Örneğin, farklı bir Kullanıcı ilgili bir eylem gerçekleştirdiğinde anında iletme bildirimleri gönderilebilir.

Anında iletme bildirimi gönderme mekanizması Blazor , her türlü teknolojiyi kullanan arka uç sunucusu tarafından uygulandığından, webassembly 'dan tamamen bağımsızdır. Bir ASP.NET Core sunucusundan anında iletme bildirimleri göndermek istiyorsanız, [güçlendirme, pizza Atölyesi Workshop ' de gerçekleştirilen yaklaşıma benzer bir teknik kullanmayı](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)düşünün.

İstemci üzerinde anında iletme bildirimi alma ve görüntüleme mekanizması Blazor , hizmet çalışanı JavaScript dosyasında uygulandığından webassembly ' den bağımsızdır. Bir örnek için bkz. [güçlendirme pizza 'da kullanılan yaklaşım](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Çevrimdışı PDI uyarıları

Tüm uygulamalar çevrimdışı kullanımı desteklemeyi denememelidir. Çevrimdışı destek, önemli karmaşıklık ekler, ancak her zaman gereken kullanım durumları için geçerli değildir.

Çevrimdışı destek genellikle ilgilidir:

* Birincil veri deposu tarayıcıya yereldir. Örneğin, yaklaşım, verileri [IoT](https://en.wikipedia.org/wiki/Internet_of_things) `localStorage` veya [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)'yi depolayan bir IoT cihazına yönelik kullanıcı arabirimine sahip bir uygulama ile ilgilidir.
* Uygulama, her kullanıcıyla ilgili arka uç API verisini getirmek ve önbelleğe almak için önemli miktarda iş gerçekleştiriyorsa, bu sayede verileri çevrimdışı olarak gezinebilirler. Uygulamanın düzenlemesini desteklemesi gerekiyorsa, değişiklikleri izlemeye yönelik bir sistem, arka uca veri eşitlemesi oluşturulmalıdır.
* Hedef ise, ağ koşullarından bağımsız olarak uygulamanın hemen yüklendiğini güvence altına almak için kullanılır. İsteklerin ilerlemesini göstermek için arka uç API 'SI isteklerinde uygun bir kullanıcı deneyimi uygulayın ve ağ kullanılamamasından kaynaklanan istekler başarısız olduğunda düzgün bir şekilde davranır.

Ayrıca, çevrimdışı yetenekli PWAs bir dizi ek karmaşıklıklarla uğraşmalıdır. Geliştiriciler, aşağıdaki bölümlerdeki uyarıları dikkatle dikkatlice bilmelidir.

### <a name="offline-support-only-when-published"></a>Yalnızca yayımlandığında çevrimdışı destek

Geliştirme sırasında, genellikle her bir değişikliğin bir arka plan güncelleştirme işlemine geçmeden hemen tarayıcıda yansıtıldığını görmek istersiniz. Bu nedenle, Blazor PWA şablonu yalnızca yayımlandığında çevrimdışı desteğe izin vermez.

Çevrimdışı özellikli bir uygulama oluştururken uygulamayı geliştirme ortamında test etmek yeterli değildir. Farklı ağ koşullarına nasıl yanıt verdiğini anlamak için uygulamayı yayımlanmış durumunda test etmeniz gerekir.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Uygulamadan sonra Kullanıcı gezindikten sonra tamamlamayı Güncelleştir

Kullanıcılar uygulamadan tüm sekmelerde gezinene kadar güncelleştirmeler tamamlanmaz. [Arka plan güncelleştirmeleri](#background-updates) bölümünde açıklandığı gibi, uygulamaya bir güncelleştirme dağıttıktan sonra, tarayıcı güncelleştirme işlemini başlatmak için güncelleştirilmiş hizmet çalışanı dosyalarını getirir.

Birçok geliştirici, bu güncelleştirme tamamlandığında bile Kullanıcı tüm sekmelerde gezinene kadar **etkili olmaz.** Uygulamayı görüntüleyen tek sekme olsa bile, uygulamayı görüntüleyen sekmenin yenilenmesi yeterli **değildir** . Uygulamanız tamamen kapanana kadar, yeni hizmet çalışanı durumu *Etkinleştirme bekleniyor* durumunda kalır. **Bu öğesine özgü değildir Blazor , ancak standart bir Web platformu davranışı olur.**

Bu, yaygın olarak çalışan geliştiricilerin hizmet çalışanlarına veya Çevrimdışı önbelleğe alınmış kaynaklara yönelik güncelleştirmeleri test kurmaya çalışıyor. Tarayıcının geliştirici araçlarını iade ederseniz aşağıdakine benzer bir şey görebilirsiniz:

![Google Chrome ' uygulama ' sekmesi, uygulamanın hizmet çalışanının ' etkinleştirilmesi bekleniyor ' olduğunu gösterir.](progressive-web-app/_static/image7.png)

Uygulamanızı görüntüleyen "istemciler" ("istemciler") listesi boş olmadığı sürece, çalışan beklemeye devam eder. Hizmet çalışanlarının bunu yapması neden olur. Tutarlılık, tüm kaynakların aynı atomik önbellekten getirileceği anlamına gelir.

Değişiklikleri test ederken, yukarıdaki ekran görüntüsünde gösterildiği gibi "Skipbeklediği" bağlantısına tıklamayı kullanışlı bulabilirsiniz, sonra sayfayı yeniden yükleyin. ["Bekleme" aşamasını atlayıp güncelleştirme üzerinde hemen etkinleştirmek](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)üzere hizmet çalışanınızı kodlayarak, tüm kullanıcılar için bunu otomatikleştirin. Bekleme aşamasını atlarsanız, kaynakların her zaman aynı önbellek örneğinden düzenli olarak getirilme garantisi vermiş olursunuz.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Kullanıcılar uygulamanın geçmiş bir sürümünü çalıştırabilir

Web geliştiricileri habitually, kullanıcıların geleneksel Web Dağıtım modelinde bu yana, Web uygulamasının en son dağıtılan sürümünü çalıştırmasını bekler. Ancak, çevrimdışı bir ilk PWA, kullanıcıların en son sürümü çalıştırması gereken yerel bir mobil uygulamaya daha fazla oturum sağlar.

[Arka plan güncelleştirmeleri](#background-updates) bölümünde açıklandığı gibi, uygulamanıza bir güncelleştirme dağıttıktan sonra, **mevcut olan her Kullanıcı en az bir daha ziyaret için daha önceki bir sürümü kullanmaya devam eder** , çünkü güncelleştirme arka planda gerçekleştiğinden ve Kullanıcı daha sonra uzaklaşana kadar etkinleştirilmemiş. Ayrıca, kullanılmakta olan önceki sürüm, dağıttığınız bir önceki sürüm değildir. Önceki sürüm, kullanıcının bir güncelleştirmeyi en son ne zaman tamamladığını bağlı *olarak geçmiş sürüm* olabilir.

Uygulamanızın ön uç ve arka uç bölümleri API istekleri için şema hakkında anlaşma gerektiriyorsa bu bir sorun olabilir. Tüm kullanıcıların yükseltildiğinden emin olana kadar, geriye dönük olarak uyumsuz API şeması değişikliklerini dağıtmamalıdır. Alternatif olarak, kullanıcıların uygulamanın uyumsuz eski sürümlerini kullanmalarını engelleyin. Bu senaryo gereksinimi, yerel mobil uygulamalar için aynıdır. Sunucu API 'Lerinde bir son değişiklik dağıtırsanız, istemci uygulaması henüz güncelleştirilmemiş kullanıcılar için bozulur.

Mümkünse, arka uç API 'lerinize son değişiklikleri dağıtmayın. Bunu yapmanız gerekiyorsa, uygulamanın güncel olup olmadığını ve kullanım dışı olduğunu anlamak için [ServiceWorkerRegistration gibi standart hizmet çalışanı API 'lerini](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) kullanmayı düşünün.

### <a name="interference-with-server-rendered-pages"></a>Sunucu tarafından işlenmiş sayfalarla girişim

[Sunucu tarafından işlenen sayfalar](#support-server-rendered-pages) bölümünde açıklandığı gibi, hizmet çalışanının `/index.html` Tüm gezinti istekleri için içerik döndürme davranışını atlamak istiyorsanız, hizmet çalışanınızdaki mantığı düzenleyin.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Tüm hizmet çalışanı varlık bildirimi içerikleri varsayılan olarak önbelleğe alınır

[Denetim varlığı önbelleğe alma](#control-asset-caching) bölümünde açıklandığı gibi, *Service-Worker-assets. js* dosyası derleme sırasında oluşturulur ve hizmet çalışanının getirmesi ve önbelleğe alınması gereken tüm varlıkları listeler.

Bu liste, dış paketler ve projeler tarafından sağlanan içerik dahil olmak üzere *Wwwroot*'a yayılan her şeyi içerdiğinden, orada çok fazla içerik yerleştirmemeye dikkat etmeniz gerekir. *Wwwroot* dizini milyonlarca görüntü içeriyorsa, hizmet çalışanı, yoğun bant genişliğine sahip ve büyük olasılıkla başarıyla tamamlanmayan bir şekilde bunları alıp önbelleğe almaya çalışır.

`onInstall` *Service-Worker. yayınlanmış. js*' de işlev düzenlenerek bildirim içeriğinin hangi alt kümesinin alınacağını ve önbelleğe alınacağını denetlemek için rastgele mantık uygulayın.

### <a name="interaction-with-authentication"></a>Kimlik doğrulamasıyla etkileşim

PWA şablonu seçeneğini kimlik doğrulama seçenekleriyle birlikte kullanmak mümkündür. Çevrimdışı özellikli bir PWA, kullanıcının ağ bağlantısı olduğunda kimlik doğrulamasını da destekleyebilir.

Bir kullanıcının ağ bağlantısı yoksa, kimlik doğrulaması yapamaz veya erişim belirteçleri elde etmez. Varsayılan olarak, ağ erişimi olmadan oturum açma sayfasını ziyaret etme girişimi, "ağ hatası" iletisiyle sonuçlanır.

Kullanıcının kimlik doğrulaması yapmaya veya erişim belirteçlerine gerek kalmadan çevrimdışıyken yararlı işlemler yapmasını sağlayan bir UI akışı tasarlamanız gerekir. Alternatif olarak, ağ kullanılabilir olmadığında, uygulamayı düzgün bir şekilde başarısız olacak şekilde tasarlayabilirsiniz. Uygulamanızda bu mümkün değilse, çevrimdışı desteği etkinleştirmek istemeyebilirsiniz.
