---
title: ASP.NET Core Blazor WebAssembly ile Aşamalı Web Uygulamaları Oluşturun
author: guardrex
description: Masaüstü uygulaması gibi Blazorgörünen modern tarayıcı özelliklerini kullanan aşamalı web uygulamasını (PWA) nasıl oluştureceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218953"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Core Blazor WebAssembly ile ASP.NET Aşamalı Web Uygulamaları Oluşturun

Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aşamalı Web Uygulaması (PWA), modern tarayıcı API'lerini ve yeteneklerini bir masaüstü uygulaması gibi kullanabilen tek sayfalık bir uygulamadır (SPA). Blazor WebAssembly standart tabanlı istemci tarafı web uygulaması platformudur, bu nedenle aşağıdaki özellikler için gerekli PWA API'leri de dahil olmak üzere herhangi bir tarayıcı API'sini kullanabilir:

* Ağ hızından bağımsız olarak çevrimdışı çalışma ve anında yükleme.
* Sadece bir tarayıcı penceresinde değil, kendi uygulama penceresinde çalışan.
* Ana bilgisayar işletim sistemi başlat menüsünden, dock veya ana ekrandan başlatılan ediliyor.
* Kullanıcı uygulamayı kullanmasa bile arka uçtan anında iletme bildirimleri alma.
* Arka planda otomatik olarak güncelleniyor.

Aşamalı *sözcük* bu tür uygulamaları tanımlamak için kullanılır, çünkü:

* Bir kullanıcı uygulamayı ilk olarak diğer SPA'lar gibi web tarayıcısı içinde keşfedebilir ve kullanabilir.
* Daha sonra, kullanıcı işletim sistemi yüklemeve anında iletme bildirimleri etkinleştirme ile ilerler.

## <a name="create-a-project-from-the-pwa-template"></a>PWA şablonundan proje oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Yeni Proje Oluştur** iletişim kutusunda yeni bir **Blazor WebAssembly Uygulaması** oluştururken, İlerleme Web **Uygulaması** onay kutusunu seçin:

![Visual Studio yeni proje iletişim kutusunda 'Aşamalı Web Uygulaması' onay kutusu seçilir.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Görsel Stüdyo Kodu / .NET Çekirdek CLI](#tab/visual-studio-code+netcore-cli)

`--pwa` Anahtarla komut kabuğunda bir PWA projesi oluşturun:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

İsteğe bağlı olarak, PWA ASP.NET Core Hosted şablonundan oluşturulan bir uygulama için yapılandırılabilir. PWA senaryosu barındırma modelinden bağımsızdır.

## <a name="installation-and-app-manifest"></a>Kurulum ve uygulama bildirimi

PWA şablonu kullanılarak oluşturulan bir uygulamayı ziyaret ederken, kullanıcılar uygulamayı işletim sistemi başlangıç menüsüne, dock'una veya ana ekranına yükleme seçeneğine sahiptir. Bu seçeneğin sunulma şekli kullanıcının tarayıcıya bağlıdır. Edge veya Chrome gibi masaüstü Krom tabanlı tarayıcıları kullanırken, URL çubuğunda **Ekle** düğmesi görüntülenir. Kullanıcı **Ekle** düğmesini seçtikten sonra bir onay iletişim kutusu alır:

![Google Chrome'daki onay diaglogu, 'MyBlazorPwa' uygulaması için kullanıcıya bir Yükle düğmesi sunar.](progressive-web-app/_static/image2.png)

iOS'ta ziyaretçiler, PWA'yı Safari'nin **Paylaş** düğmesini ve **Ana Ekrana Ekle** seçeneğini kullanarak yükleyebilir. Android için Chrome'da, kullanıcılar sağ üst köşedeki **Menü** düğmesini seçerek **Ana Ekrana Ekle'yi**seçmelidir.

Yüklendikten sonra, uygulama adres çubuğu olmadan kendi penceresinde görünür:

!['MyBlazorPwa' uygulaması, adres çubuğu olmadan Google Chrome'da çalışır.](progressive-web-app/_static/image3.png)

Pencerenin başlığını, renk düzenini, simgesini veya diğer ayrıntıları özelleştirmek için projenin *wwwroot* dizinindeki *manifest.json* dosyasına bakın. Bu dosyanın şeması web standartlarına göre tanımlanır. Daha fazla bilgi için [MDN web dokümanlarına bakın: Web App Manifest.](https://developer.mozilla.org/docs/Web/Manifest)

## <a name="offline-support"></a>Çevrimdışı destek

Varsayılan olarak, PWA şablonu seçeneği kullanılarak oluşturulan uygulamaların çevrimdışı çalıştırmak için desteği olur. Bir kullanıcının ilk olarak uygulamayı çevrimiçi yken ziyaret etmesi gerekir. Tarayıcı çevrimdışı çalıştırmak için gereken tüm kaynakları otomatik olarak karşıdan yükler ve önbelleğe aler.

> [!IMPORTANT]
> Geliştirme desteği, değişiklikler yapma ve bunları test etme olağan geliştirme döngüsünü engelleyecektir. Bu nedenle, çevrimdışı destek yalnızca yayınlanan uygulamalar için *etkinleştirilir.* 

> [!WARNING]
> Çevrimdışı olarak etkin bir PWA dağıtmayı planlıyorsanız, [birkaç önemli uyarı ve uyarı](#caveats-for-offline-pwas)vardır. Bu senaryolar çevrimdışı PWA'lara özgüdir Blazorve özel değildir. Çevrimdışı özellikli uygulamanızın nasıl çalışacağı hakkında varsayımlarda bulunmadan önce bu uyarıları okuduğunuzdan ve anladığınızdan emin olun.

Çevrimdışı desteğin nasıl çalıştığını görmek için:

1. Uygulamayı yayınlayın. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Uygulamayı HTTPS'yi destekleyen bir sunucuya dağıtın ve uygulamanın güvenli HTTPS adresinden bir tarayıcıda erişin.
1. Tarayıcının dev araçlarını açın ve Bir *Hizmet Çalışanının* **Uygulama** sekmesinde ana bilgisayar için kayıtlı olduğunu doğrulayın:

   ![Google Chrome geliştirici araçları 'Uygulama' sekmesi, etkinleştirilen ve çalışan bir Servis Çalışanı'nı gösterir.](progressive-web-app/_static/image4.png)

1. Sayfayı yeniden yükleyin ve **Ağ** sekmesini inceleyin. **Hizmet Çalışanı** veya **bellek önbelleği** sayfanın tüm varlıklarının kaynağı olarak listelenir:

   ![Google Chrome geliştirici araçları 'Ağ' sekmesi, sayfanın tüm varlıklarıiçin kaynakları gösterir.](progressive-web-app/_static/image5.png)

1. Uygulamayı yüklemek için tarayıcının ağ erişimine bağlı olmadığını doğrulamak için aşağıdakileri de:

   * Web sunucusunu kapatın ve uygulamanın sayfa yeniden yüklemelerini içeren normal şekilde nasıl çalışmaya devam ettiğine bakın. Aynı şekilde, yavaş bir ağ bağlantısı olduğunda uygulama normal şekilde çalışmaya devam eder.
   * Tarayıcıya **Ağ** sekmesinde çevrimdışı modu simüle etmesini öğretin:

   ![Tarayıcı modu ile Google Chrome geliştirici araçları 'Ağ' sekmesi 'Çevrimiçi' 'Çevrimdışı' olarak değiştiriliyor.](progressive-web-app/_static/image6.png)

Bir hizmet çalışanı kullanarak çevrimdışı destek bir web Blazorstandardı, belirli değildir. Hizmet çalışanları hakkında daha fazla bilgi için [MDN web dokümanlarına bakın: Servis Görevlisi API' si.](https://developer.mozilla.org/docs/Web/API/Service_Worker_API) Hizmet çalışanları için yaygın kullanım alışkanlıkları hakkında daha fazla bilgi edinmek için [Google Web: Hizmet Çalışanı Yaşam Döngüsü'ne](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)bakın.

Blazor'S PWA şablonu iki hizmet çalışanı dosyası üretir:

* geliştirme sırasında kullanılan *wwwroot/service-worker.js.*
* *wwwroot/service-worker.published.js*, uygulama yayınlandıktan sonra kullanılır.

İki hizmet çalışanı dosyası arasında mantık paylaşmak için aşağıdaki yaklaşımı göz önünde bulundurun:

* Ortak mantığı tutmak için üçüncü bir JavaScript dosyası ekleyin.
* Ortak mantığı her iki servis çalışanı dosyasına yüklemek için [self.importScripts'i](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) kullanın.

### <a name="cache-first-fetch-strategy"></a>Önbellek-ilk getirme stratejisi

Yerleşik hizmet *çalışanı.published.js* servis çalışanı istekleri *önbellek ilk* stratejisini kullanarak çözer. Bu, kullanıcının ağ erişimine sahip olup olmadığına veya sunucuda yeni içerik bulunup bulunmadığına bakılmaksızın, hizmet çalışanının önbelleğe alınmış içeriği döndürmeyi tercih ettiği anlamına gelir.

Önbellek ilk stratejisi değerlidir, çünkü:

* **Güvenilirliği sağlar.** &ndash;Ağ erişimi boolean durumu değildir. Bir kullanıcı yalnızca çevrimiçi veya çevrimdışı değildir:

  * Kullanıcının aygıtı çevrimiçi olduğunu varsayabilir, ancak ağ beklemek için pratik olacak kadar yavaş olabilir.
  * Ağ, belirli istekleri engelleyen veya yeniden yönlendiren bir tutsak WIFI portalı olması gibi belirli URL'ler için geçersiz sonuçlar döndürebilir.
  
  Bu nedenle tarayıcının `navigator.onLine` API'si güvenilir değildir ve bağımlı olmamalıdır.

* **Doğruluğu sağlar.** &ndash;Hizmet çalışanı, çevrimdışı kaynakların önbelleğini yaparken, kaynakların tek bir anda tam ve kendi kendine tutarlı anlık görüntüsünü aldığını garanti etmek için içerik karmalarını kullanır. Bu önbellek daha sonra atomik birim olarak kullanılır. Gereken tek sürümler zaten önbelleğe alınmış olanlardır, yeni kaynaklar için ağ dan istemenin bir anlamı yoktur. Başka bir şey tutarsızlık ve uyumsuzluk riski (örneğin, birlikte derlenmemiş .NET derlemelerinin sürümlerini kullanmaya çalışmak).

### <a name="background-updates"></a>Arka plan güncellemeleri

Bir zihinsel model olarak, çevrimdışı ilk PWA'nın yüklenebilecek bir mobil uygulama gibi davrandığını düşünebilirsiniz. Uygulama, ağ bağlantısından bağımsız olarak hemen başlar, ancak yüklenen uygulama mantığı en son sürüm olmayan bir anlık görüntüden gelir.

PWA şablonu, Blazor kullanıcı ziyaret ettiğinde ve çalışan bir ağ bağlantısı olduğunda kendilerini arka planda otomatik olarak güncellemeye çalışan uygulamalar üretir. Bu çalışma şekli aşağıdaki gibidir:

* Derleme sırasında, proje bir *hizmet çalışanı varlıkları tezahür oluşturur.* Varsayılan olarak, bu *hizmet-işçi-varlıklar.js*denir. Bildirim, uygulamanın çevrimdışı çalışması için gereken .NET derlemeleri, JavaScript dosyaları ve css gibi içerik zorlukları da dahil olmak üzere gereken tüm statik kaynakları listeler. Kaynak listesi, hangi kaynakların önbelleğe alındığını bilmesi için servis çalışanı tarafından yüklenir.
* Kullanıcı uygulamayı her ziyaret edinde, tarayıcı arka planda *servis-worker.js* ve *hizmet-worker-assets.js'yi* yeniden talep eder. Dosyalar, varolan yüklenen servis çalışanı ile bayt-byte karşılaştırılır. Sunucu bu dosyalardan biri için değiştirilen içeriği döndürürse, servis çalışanı kendi sürümünü yüklemeye çalışır.
* Kendi yeni bir sürümünü yüklerken, hizmet çalışanı çevrimdışı kaynaklar için yeni, ayrı bir önbellek oluşturur ve önbelleği *hizmet çalışanı-varlıklar.js'de*listelenen kaynaklarla doldurmaya başlar. Bu mantık `onInstall` *hizmet-worker.published.js*içinde işlevi nde uygulanır.
* Tüm kaynaklar hatasız yüklendiğinde ve tüm içerik kalıpları eşleştiğinde işlem başarıyla tamamlanır. Başarılı olursa, yeni hizmet çalışanı etkinleştirme durumunu *bekliyor.* Kullanıcı uygulamayı kapatır kapatmaz (kalan uygulama sekmeleri veya pencereler için bulunmaz), yeni servis çalışanı *etkin* hale gelir ve sonraki uygulama ziyaretleri için kullanılır. Eski servis çalışanı ve önbelleği silinir.
* İşlem başarıyla tamamlanmazsa, yeni servis çalışanı örneği atılır. Güncelleştirme işlemi, istemcinin istekleri tamamlayabilecek daha iyi bir ağ bağlantısına sahip olması yla kullanıcının bir sonraki ziyaretinde yeniden denenir.

Hizmet çalışanı mantığını düzenleyerek bu işlemi özelleştirin. Önceki davranışların hiçbiri belirli Blazor değildir, ancak yalnızca PWA şablonu seçeneği tarafından sağlanan varsayılan deneyimdir. Daha fazla bilgi için [BKZ: MDN web dokümanları: Servis Görevlisi API.](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)

### <a name="how-requests-are-resolved"></a>İstekler nasıl çözülür?

[Önbellek-ilk getirme stratejisi](#cache-first-fetch-strategy) bölümünde açıklandığı gibi, varsayılan hizmet çalışanı *önbellek ilk* stratejisi kullanır, yani önbelleğe alınmış içeriği kullanılabilir olduğunda sunmaya çalışır. Belirli bir URL için önbelleğe alınmış içerik yoksa (örneğin, arka uç API'den veri istenirken), servis çalışanı normal bir ağ isteğine geri döner. Sunucuya erişilebilirse ağ isteği başarılı olur. Bu mantık `onFetch` *hizmet-worker.published.js*içinde işlevi içinde uygulanır.

Uygulamanın Razor bileşenleri arka uç API'lerinden veri istemeye güveniyorsa ve ağ kullanılabilirliği nedeniyle başarısız istekler için kolay bir kullanıcı deneyimi sağlamak istiyorsanız, uygulamanın bileşenleri içindeki mantığı uygulayın. Örneğin, istekler `HttpClient` etrafında kullanın. `try/catch`

### <a name="support-server-rendered-pages"></a>Sunucu tarafından işlenen sayfaları destekleme

Kullanıcı, uygulamadaki herhangi bir derin bağlantı `/counter` gibi bir URL'ye ilk gittiğinde neler olacağını düşünün. Bu gibi durumlarda, önbelleğe alınmış içeriği döndürmek `/counter`istemezsiniz, ancak bunun yerine WebAssembly `/index.html` uygulamanızı Blazor başlatmak için önbelleğe alınmış içeriği yüklemek için tarayıcıya ihtiyacınız vardır. Bu ilk istekler, şunları aksine *gezinti* istekleri olarak bilinir:

* resimler, stil sayfaları veya diğer dosyalar için *alt kaynak* istekleri.
* API verileri için *alma/XHR* istekleri.

Varsayılan hizmet çalışanı, gezinti istekleri için özel durum mantığı içerir. Hizmet çalışanı, istenen URL'den bağımsız olarak önbelleğe alınmış içeriği `/index.html`döndürerek istekleri giderir. Bu mantık `onFetch` *hizmet-worker.published.js*içinde işlevi nde uygulanır.

Uygulamanızda sunucu tarafından işlenen HTML döndürülmesi gereken ve önbellekten hizmet `/index.html` alamayan belirli URL'ler varsa, servis çalışanınızdaki mantığı düzenlemeniz gerekir. İçerdiği `/Identity/` tüm URL'lerin sunucuya düzenli çevrimiçi istekolarak ele alınması gerekiyorsa, *service-worker.published.js* `onFetch` mantığını değiştirin. Aşağıdaki kodu bulun:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Kodu aşağıdakiyle değiştirin:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Bunu yapmazsanız, ağ bağlantısından bağımsız olarak, servis çalışanı bu tür URL'ler için isteklerini `/index.html`keser ve bunları kullanarak çözer.

### <a name="control-asset-caching"></a>Varlık önbelleğe alma denetimi

Projeniz MSBuild `ServiceWorkerAssetsManifest` özelliğini tanımlıyorsa, Blazor'yapı aracı, belirtilen ada sahip bir hizmet çalışanı varlıkları tezahür oluşturur. Varsayılan PWA şablonu aşağıdaki özelliği içeren bir proje dosyası üretir:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Dosya *wwwroot* çıktı dizini yerleştirilir, böylece tarayıcı isteyerek `/service-worker-assets.js`bu dosyayı alabilirsiniz. Bu dosyanın içeriğini görmek için bir metin düzenleyicisinde */bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js'i* açın. Ancak, her yapıda yeniden oluşturulduğu için dosyayı dalamayın.

Varsayılan olarak, bu bildirim listeler:

* .NET derlemeleri ve çevrimdışı çalışması için gereken .NET WebAssembly çalışma zamanı dosyaları gibi yönetilen tüm Blazorkaynaklar.
* Harici projeler ve NuGet paketleri tarafından sağlanan statik web varlıkları da dahil olmak üzere, resimler, stil sayfaları ve JavaScript dosyaları gibi uygulamanın *wwwroot* dizininde yayımlanmak için tüm kaynaklar.

Bu kaynaklardan hangisinin servis çalışanı tarafından getirilip önbelleğe alınmış `onInstall` *olduğunu, hizmet işçisi.published.js'deki*mantığı düzenleyerek denetleyebilirsiniz. Varsayılan olarak, hizmet çalışanı *.html*, *.css*, *.js*ve *.wasm*, ayrıca Blazor WebAssembly ' e özgü dosya türleri (*.dll*, *.pdb)* gibi tipik web dosya adı uzantılarıyla eşleşen dosyaları getirir ve önbelleğe alır.

Uygulamanın *wwwroot* dizininde bulunmayan ek kaynaklar eklemek için, aşağıdaki örnekte gösterildiği gibi ek MSBuild `ItemGroup` girişleri tanımlayın:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Meta `AssetUrl` veriler, kaynağı önbelleğe alırken tarayıcının kullanması gereken temel göreli URL'yi belirtir. Bu, diskteki özgün kaynak dosya adından bağımsız olabilir.

> [!IMPORTANT]
> A `ServiceWorkerAssetsManifestItem` eklemek, dosyanın uygulamanın *wwwroot* dizininde yayınlanmasına neden olmaz. Yayımlama çıktısı ayrı olarak denetlenmelidir. Yalnızca `ServiceWorkerAssetsManifestItem` ek bir girişin hizmet çalışanı varlıklar bildiriminde görünmesine neden olur.

## <a name="push-notifications"></a>Anında iletme bildirimleri

Diğer PWA'lar Blazor gibi, bir WebAssembly PWA'sı da arka uç sunucusundan anında iletme bildirimleri alabilir. Sunucu, kullanıcı uygulamayı etkin olarak kullanmasa bile istediği zaman anında iletme bildirimleri gönderebilir. Örneğin, farklı bir kullanıcı ilgili bir eylem gerçekleştirdiğinde anında iletme bildirimleri gönderilebilir.

Anında iletme bildirimi gönderme mekanizması, Blazor herhangi bir teknolojiyi kullanabilen arka uç sunucusu tarafından uygulandığından, WebAssembly'den tamamen bağımsızdır. Bir ASP.NET Core sunucusundan anında iletme bildirimleri göndermek istiyorsanız, [Blazing Pizza atölyesinde alınan yaklaşıma benzer bir teknik kullanmayı](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)düşünün.

Hizmet çalışanı JavaScript dosyasında uygulandığından, istemciye anında Blazor iletme bildirimi alma ve görüntüleme mekanizması da WebAssembly'den bağımsızdır. Örneğin, [Blazing Pizza atölyesinde kullanılan yaklaşıma](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)bakın.

## <a name="caveats-for-offline-pwas"></a>Çevrimdışı PWA'lar için uyarılar

Tüm uygulamalar çevrimdışı kullanımı desteklemeye çalışmamalıdır. Çevrimdışı destek, her zaman gerekli kullanım örnekleri için alakalı olmamakla birlikte önemli bir karmaşıklık ekler.

Çevrimdışı destek genellikle yalnızca alakalıdır:

* Birincil veri deposu tarayıcıya yerelse. Örneğin, yaklaşım, verileri `localStorage` depolayan bir IoT aygıtı veya [IndexedDB'nin](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)bir [uot](https://en.wikipedia.org/wiki/Internet_of_things) aygıtına sahip bir uygulamada alakalıdır.
* Uygulama, verileri çevrimdışı olarak gezinmeleri için her kullanıcıyla alakalı arka uç API verilerini almak ve önbelleğe almak için önemli miktarda çalışma yaparsa. Uygulamanın düzenlemeyi desteklemesi gerekiyorsa, değişiklikleri izlemek ve verileri arka uçla eşitlemek için bir sistem oluşturulmalıdır.
* Amaç, uygulamanın ağ koşullarından bağımsız olarak hemen yüklediğini garanti etmekse. Taleplerin ilerlemesini göstermek ve ağ kullanılabilirliği nedeniyle istekleri başarısız olduğunda incelikle hareket etmek için arka uç API istekleri etrafında uygun bir kullanıcı deneyimi uygulayın.

Ayrıca, çevrimdışı özellikli PWA'lar bir dizi ek komplikasyonla uğraşmak zorundadır. Geliştiriciler dikkatle aşağıdaki bölümlerde uyarılar ile kendilerini tanımak gerekir.

### <a name="offline-support-only-when-published"></a>Yalnızca yayımlandığında çevrimdışı destek

Geliştirme sırasında genellikle her değişikliğin arka plan güncelleştirme işleminden geçmeden tarayıcıya hemen yansıtTığını görmek istersiniz. Bu Blazornedenle, 'PWA şablonu yalnızca yayımlandığında çevrimdışı destek sağlar.

Çevrimdışı özellikli bir uygulama yaparken, uygulamayı Geliştirme ortamında sınamak yeterli değildir. Uygulamayı farklı ağ koşullarına nasıl yanıt veriyi anlamak için yayımlanmış durumunda sınamalısınız.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Kullanıcı uygulamasından uygulamadan uzaklaştıktan sonra tamamlanmayı güncelleştirme

Kullanıcı tüm sekmelerde uygulamadan uzaklaşana kadar güncellemeler tamamlanmaz. Arka Plan [güncellemeleri](#background-updates) bölümünde açıklandığı gibi, uygulamaya bir güncelleştirme dağıttıktan sonra, tarayıcı güncelleştirme işlemini başlatmak için güncelleştirilmiş servis çalışanı dosyalarını getirir.

Birçok geliştiriciyi şaşırtan şey, bu güncelleştirme tamamlandığında bile, kullanıcı tüm sekmelerde gezinene kadar etkili **olmamasıdır.** Uygulamayı görüntüleyen tek sekme olsa bile, uygulamayı görüntüleyen sekmeyi yenilemek yeterli **değildir.** Uygulamanız tamamen kapatılana kadar, yeni servis çalışanı durumu *etkinleştirmek için beklemede* kalır. **BlazorBu, belirli değildir, ancak daha ziyade standart bir web platformu davranışıdır.**

Bu genellikle, hizmet çalışanları veya çevrimdışı önbelleğe alınmış kaynakların güncelleştirmelerini sınamaya çalışan geliştiricileri rahatsız ediyor. Tarayıcının geliştirici araçlarını iade ederseniz, aşağıdaki gibi bir şey görebilirsiniz:

![Google Chrome 'Uygulama' sekmesi, uygulamanın Servis Görevlisi'nin 'etkinleştirmeyi beklediğini' gösterir.](progressive-web-app/_static/image7.png)

Uygulamanızı görüntüleyen sekmeler veya pencereler olan "istemciler" listesi boş değilken, çalışan beklemeye devam eder. Servis çalışanlarının bunu yapmasının nedeni tutarlılığı garanti etmektir. Tutarlılık, tüm kaynakların aynı atomik önbellekten geldiği anlamına gelir.

Değişiklikleri sınarken, önceki ekran görüntüsünde gösterildiği gibi "skipWaiting" bağlantısını tıklatıp sayfayı yeniden yüklemeyi uygun bulabilirsiniz. Bunu tüm kullanıcılar için ["bekleme" aşamasını atlamak ve güncelleştirmede hemen etkinleştirmek](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)için servis çalışanınızı kodlayarak otomatikleştirebilirsiniz. Bekleme aşamasını atlarsanız, kaynakların her zaman aynı önbellek örneğinden sürekli olarak getirilme garantisinden vazgeçmiş olursunuz.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Kullanıcılar uygulamanın herhangi bir tarihsel sürümünü çalıştırabilir

Web geliştiricileri, geleneksel web dağıtım modelinde normal olduğundan, kullanıcıların yalnızca web uygulamalarının en son dağıtılan sürümünü çalıştırmalarını beklerler. Ancak, çevrimdışı ilk PWA, kullanıcıların en son sürümü çalıştırmadığı yerel bir mobil uygulamaya daha çok benzer.

Arka Plan [güncellemeleri](#background-updates) bölümünde açıklandığı gibi, uygulamanıza bir güncelleştirme dağıttıktan sonra, güncelleme arka planda gerçekleştiği nden ve kullanıcı daha sonra uzaklaşana kadar etkinleştirilmediği **için, her varolan kullanıcı en az bir ziyaret için önceki sürümü kullanmaya devam eder.** Ayrıca, kullanılan önceki sürüm, dağıttığınız önceki sürüm olması gerekmez. Önceki sürüm, kullanıcının bir güncelleştirmeyi en son ne zaman tamamladıkladığına bağlı olarak *geçmiş* bir sürüm olabilir.

Uygulamanızın ön ve arka uç bölümleri API istekleri için şema hakkında anlaşma gerektiriyorsa, bu bir sorun olabilir. Tüm kullanıcıların yükselttiğini emin olana kadar geriye dönük uyumsuz API şeması değişiklikleri dağıtmamalısınız. Alternatif olarak, kullanıcıların uygulamanın uyumsuz eski sürümlerini kullanmasını engelleyin. Bu senaryo gereksinimi, yerel mobil uygulamalarla aynıdır. Sunucu API'lerinde bir son dakika değişikliği dağıtırsanız, istemci uygulaması henüz güncellenmemiş kullanıcılar için bozukolur.

Mümkünse, arka uç API'lerinizde çığır açan değişiklikleri dağıtmayın. Bunu yapmanız gerekiyorsa, uygulamanın güncel olup olmadığını ve kullanılmayacağını belirlemek için [ServiceWorkerRegistration gibi standart Servis Görevlisi API'lerini](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) kullanmayı düşünün.

### <a name="interference-with-server-rendered-pages"></a>Sunucu tarafından işlenen sayfalara müdahale

Destek sunucusu [tarafından işlenen sayfalar](#support-server-rendered-pages) bölümünde açıklandığı gibi, servis çalışanının tüm gezinti istekleri `/index.html` için iade edilen içeriği davranışını atlamak istiyorsanız, servis çalışanınızdaki mantığı düzenleme.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Tüm hizmet çalışanı varlık bildirimi içeriği varsayılan olarak önbelleğe alınır

Denetim varlık [önbelleğe alma](#control-asset-caching) bölümünde açıklandığı gibi, dosya *hizmeti-işçi-varlıklar.js* oluşturma sırasında oluşturulur ve hizmet çalışanının getirmesi ve önbelleğe alması gereken tüm varlıkları listeler.

Varsayılan olarak bu liste, harici paketler ve projeler tarafından sağlanan içerik de dahil olmak üzere *wwwroot'a*yayılan her şeyi içerdiğinden, oraya çok fazla içerik koymamaya dikkat etmelisiniz. *wwwroot* dizini milyonlarca görüntü içeriyorsa, servis çalışanı hepsini almaya ve önbelleğe almaya çalışır, aşırı bant genişliği tüketir ve büyük olasılıkla başarılı bir şekilde tamamlanmaz.

Bildirimin içeriğinin hangi alt kümesinin alınması ve *hizmet-worker.published.js* `onInstall` işlevi düzenlenerek önbelleğe alınması gerektiğini denetlemek için rasgele mantık uygulayın.

### <a name="interaction-with-authentication"></a>Kimlik doğrulama ile etkileşim

Kimlik doğrulama seçenekleriyle birlikte PWA şablonu seçeneğini kullanmak mümkündür. Çevrimdışı özellikli bir PWA, kullanıcı ağ bağlantısına sahipolduğunda kimlik doğrulamayı da destekleyebilir.

Bir kullanıcıağ bağlantısı yoksa, kimlik doğrulaması yapamaz veya erişim belirteçleri alamaz. Varsayılan olarak, ağ erişimi olmadan giriş sayfasını ziyaret etmeye çalışmak bir "ağ hatası" iletisi ile sonuçlanır.

Kullanıcının kimlik doğrulaması veya erişim belirteçleri elde etmeye çalışmadan çevrimdışı yken yararlı şeyler yapmasına olanak tanıyan bir Kullanıcı Arabirimi akışı tasarlamanız gerekir. Alternatif olarak, ağ kullanılamadığında uygulamayı zarif bir şekilde başarısız olacak şekilde tasarlayabilirsiniz. Bu uygulamanızda mümkün değilse, çevrimdışı desteği etkinleştirmek istemeyebilirsiniz.
