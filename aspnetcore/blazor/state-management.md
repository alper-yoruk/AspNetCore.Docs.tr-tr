---
title: ASP.NET Core Blazor durum yönetimi
author: guardrex
description: Uygulamalarda durumu kalıcı hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
no-loc:
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 43794fad36efe44cad6fbb2f1a1cae293a2ddad1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625966"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>ASP.NET Core Blazor durum yönetimi

Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

Bir uygulamada oluşturulan kullanıcı durumu Blazor WebAssembly tarayıcının belleğinde tutulur.

Tarayıcı belleğinde tutulan Kullanıcı durumu örnekleri şunları içerir:

* Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.
* Bileşen örneklerinde alanların ve özelliklerin değerleri.
* [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.
* [JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla ayarlanan değerler.

Kullanıcı tarayıcısını kapatıp yeniden açtığında veya sayfayı yeniden yüklediğinde, tarayıcının belleğinde tutulan Kullanıcı durumu kaybedilir.

## <a name="persist-state-across-browser-sessions"></a>Tarayıcı oturumlarında durumu kalıcı yap

Genellikle, kullanıcıların, zaten mevcut olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu tarayıcı oturumlarında durumu koruyun.

Tarayıcı oturumlarında durumu korumak için, uygulamanın verileri tarayıcı belleğinden başka bir depolama konumuna kalıcı hale vermelidir. Durum kalıcılığı otomatik değildir. Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.

Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir. Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:

* Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır. Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.
* Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir. Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.

Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler. Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir. Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir. Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.

## <a name="where-to-persist-state"></a>Durumun nerede kalıcı olduğu

Kalıcı durum için üç ortak konum mevcuttur:

* [Sunucu tarafı depolama](#server-side-storage)
* [URL](#url)
* [Tarayıcı depolama](#browser-storage)

### <a name="server-side-storage"></a>Sunucu tarafı depolama

Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama bir Web API 'SI aracılığıyla erişilen bağımsız sunucu tarafı depolama kullanabilir. Seçeneklere şunlar dahildir:

* Blob depolama
* Anahtar-değer depolaması
* İlişkisel veritabanı
* Tablo depolama

Veriler kaydedildikten sonra, kullanıcının durumu korunur ve herhangi bir yeni tarayıcı oturumunda kullanılabilir.

Blazor WebAssemblyUygulamalar tamamen kullanıcının tarayıcısında çalıştığı için, depolama hizmetleri ve veritabanları gibi güvenli dış sistemlere erişmek için ek ölçüler gerektirir. Blazor WebAssembly uygulamalar, tek sayfalı uygulamalarla (maça 'Lar) aynı şekilde güvenli hale getirilir. Genellikle, bir uygulama [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) aracılığıyla bir kullanıcının kimliğini doğrular ve ardından sunucu tarafı bir uygulamaya Web API çağrıları aracılığıyla depolama hizmetleri ve veritabanlarıyla etkileşime girer. Sunucu tarafı uygulama, Blazor WebAssembly uygulama ve depolama hizmeti ya da veritabanı arasında veri aktarımını gösterir. Sunucu tarafı uygulamasının Blazor WebAssembly depolamaya kalıcı bir bağlantısı olduğunda uygulama, sunucu tarafı uygulamasına kısa ömürlü bir bağlantı sağlar.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*Güvenlik ve Identity * makaleleri

Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Azure veritabanları](https://azure.microsoft.com/product-categories/databases/)
* [Azure depolama belgeleri](/azure/storage/)

### <a name="url"></a>URL

Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin. URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:

* Görüntülenen varlığın KIMLIĞI.
* Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.

Kullanıcı sayfayı el ile yeniden yüklediğinde tarayıcının adres çubuğunun içeriği korunur.

Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..

### <a name="browser-storage"></a>Tarayıcı depolama

Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:

* `localStorage` tarayıcı penceresinin kapsamına alınır. Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir. Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır. Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.
* `sessionStorage` , tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir. Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir. Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.

> [!NOTE]
> `localStorage` ve `sessionStorage` Blazor WebAssembly yalnızca özel kod yazarak veya üçüncü taraf bir paket kullanılarak uygulamalarda kullanılabilir.

Genellikle, `sessionStorage` kullanmak daha güvenlidir. `sessionStorage` bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:

* Sekmelerde durum depolamadaki hatalar.
* Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.

`localStorage` uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.

> [!WARNING]
> Kullanıcılar ve içinde depolanan verileri görüntüleyebilir veya bunlarla karşılaşabilir `localStorage` `sessionStorage` .

## <a name="additional-resources"></a>Ek kaynaklar

* [Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server durum bilgisi olan bir uygulama çerçevesidir. Çoğu zaman, uygulama sunucusuyla bir bağlantı sağlar. Kullanıcının durumu, sunucu belleğinde bir *devrende*tutulur. 

Bir devreye sahip kullanıcı durumu örnekleri şunları içerir:

* Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.
* Bileşen örneklerinde alanların ve özelliklerin değerleri.
* Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.

Kullanıcı durumu, [JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla tarayıcının bellek kümesindeki JavaScript değişkenlerinde de bulunabilir.

Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor özgün durumlarındaki kullanıcıyı özgün devresine yeniden bağlamaya çalışır. Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:

* Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez. Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.
* Çok sunuculu, yük dengeli dağıtım ortamlarında, tek tek sunucular başarısız olabilir veya artık tüm istek hacminin işlenmesi gerekmiyorsa otomatik olarak kaldırılabilir. Kullanıcı yeniden bağlanmayı denediğinde, bir kullanıcı için özgün sunucu işleme istekleri kullanılamaz hale gelebilir.
* Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır. Örneğin, JavaScript birlikte çalışma çağrıları aracılığıyla ayarlanan JavaScript değişken değerleri kaybolur.

Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır. Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.

## <a name="persist-state-across-circuits"></a>Devreler arasında durumu kalıcı yap

Genellikle, kullanıcıların, zaten mevcut olan verileri okumadan değil, etkin bir şekilde veri oluşturmakta olduğu devrelerde durumu koruyun.

Uygulama, durumu devrelerde korumak için, verilerin sunucu belleğinden başka bir depolama konumuna kalıcı hale gelmelidir. Durum kalıcılığı otomatik değildir. Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.

Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir. Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:

* Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır. Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.
* Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir. Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.

Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler. Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir. Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir. Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.

## <a name="where-to-persist-state"></a>Durumun nerede kalıcı olduğu

Kalıcı durum için üç ortak konum mevcuttur:

* [Sunucu tarafı depolama](#server-side-storage)
* [URL](#url)
* [Tarayıcı depolama](#browser-storage)

### <a name="server-side-storage"></a>Sunucu tarafı depolama

Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama sunucu tarafı depolama kullanabilir. Seçeneklere şunlar dahildir:

* Blob depolama
* Anahtar-değer depolaması
* İlişkisel veritabanı
* Tablo depolama

Veriler kaydedildikten sonra, kullanıcının durumu korunur ve yeni bir devrede kullanılabilir.

Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Azure veritabanları](https://azure.microsoft.com/product-categories/databases/)
* [Azure depolama belgeleri](/azure/storage/)

### <a name="url"></a>URL

Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin. URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:

* Görüntülenen varlığın KIMLIĞI.
* Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.

Tarayıcının adres çubuğunun içeriği korunur:

* Kullanıcı sayfayı el ile yeniden yükler.
* Web sunucusu kullanılamaz hale gelirse ve Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.

Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..

### <a name="browser-storage"></a>Tarayıcı depolama

Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:

* `localStorage` tarayıcı penceresinin kapsamına alınır. Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir. Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır. Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.
* `sessionStorage` , tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir. Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir. Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.

Genellikle, `sessionStorage` kullanmak daha güvenlidir. `sessionStorage` bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:

* Sekmelerde durum depolamadaki hatalar.
* Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.

`localStorage` uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.

Tarayıcı depolamayı kullanmaya yönelik uyarılar:

* Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.
* Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.
* Birkaç kilobayt veri depolaması, uygulamalar için kalıcı hale getirilmesi mantıklıdır Blazor Server . Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.
* Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir. [ASP.NET Core veri koruma](xref:security/data-protection/introduction) riski azaltabilirler. Örneğin, [ASP.NET Core korumalı tarayıcı depolama](#aspnet-core-protected-browser-storage) ASP.NET Core veri koruma kullanır.

Üçüncü taraf NuGet paketleri ve ile çalışmaya yönelik API 'Ler `localStorage` sağlar `sessionStorage` . [ASP.NET Core veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor. Veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riski azaltır. JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir. Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir. Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir. Kullanıcıların *hassas verileri*incelemesine veya değiştirmesine izin vermeyi önleyin.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core korumalı tarayıcı depolaması

ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!NOTE]
> Korumalı tarayıcı depolaması ASP.NET Core veri korumasına dayanır ve yalnızca uygulamalar için desteklenir Blazor Server .

### <a name="configuration"></a>Yapılandırma

1. Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) .
1. `Startup.ConfigureServices`' De, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Bir bileşen içindeki verileri kaydetme ve yükleme

Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır. Aşağıdaki örnekte, `sessionStorage` kullanılır:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`Yönergesi `_Imports.razor` bileşen yerine uygulamanın dosyasına yerleştirilebilir. `_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.

`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur. Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır. `ProtectedSessionStore` karmaşık durum nesnelerini depolamak için JSON verilerini otomatik olarak serileştirir ve seri hale getirir.

Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır. Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur. Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.

`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için, `Counter` kullanıcının yeni bir devrede olması dahil, şunu kullanın `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Bileşenin parametreleri gezinti durumu içeriyorsa, çağrısı `ProtectedSessionStore.GetAsync` `null` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> yapın ve not edin <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> yalnızca bileşen ilk kez oluşturulduğunda çağrılır. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz. Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.

> [!WARNING]
> Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır. Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.
>
> Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin. Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.

### <a name="handle-the-loading-state"></a>Yükleme durumunu işle

Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır. En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.

Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir. Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` . Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :

```csharp
private int? currentCount;
```

Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler denetlenerek yüklenirse görüntüleyin <xref:System.Nullable%601.HasValue%2A> :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Tanıtıcı prerendering

Prerendering sırasında:

* Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.
* Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.

`localStorage` veya `sessionStorage` prerendering sırasında kullanılamaz. Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.

Hatayı çözmek için bir yol prerendering devre dışı bırakılır. Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir. Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .

Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` . Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin. Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Durum korumasını ortak bir konuma ayırın

Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur. Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır. Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.

Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.

Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın. Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir. Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.

Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir. Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.

Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:

* Birçok bileşen genelinde durumu kullanmak için.
* Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.

Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Korumalı tarayıcı depolaması deneysel NuGet paketi

ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` , desteklenmeyen, deneysel bir paket, üretim kullanımı için uygun değil.
>
> Paket yalnızca ASP.NET Core 3,1 Blazor Server uygulamalarında kullanılabilir.

### <a name="configuration"></a>Yapılandırma

1. Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. `Pages/_Host.cshtml`Dosyasında, kapanış etiketinin içine aşağıdaki betiği ekleyin `</body>` :

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. `Startup.ConfigureServices`' De, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Bir bileşen içindeki verileri kaydetme ve yükleme

Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır. Aşağıdaki örnekte, `sessionStorage` kullanılır:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`İfade, `_Imports.razor` bileşen yerine bir dosyaya yerleştirilebilir. `_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.

`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur. Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır. `ProtectedSessionStore` JSON verilerini otomatik olarak serileştirir ve seri hale getirir.

Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır. Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur. Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.

`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için `Counter` , tamamen yeni bir devrede olanlar da dahil olmak üzere şunu kullanın `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Bileşenin parametreleri gezinti durumu içeriyorsa, `ProtectedSessionStore.GetAsync` sonucunu çağırın ve ' de atayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> yalnızca bileşen ilk kez oluşturulduğunda çağrılır. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz. Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.

> [!WARNING]
> Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır. Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.
>
> Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin. Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.

### <a name="handle-the-loading-state"></a>Yükleme durumunu işle

Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır. En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.

Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir. Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` . Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :

```csharp
private int? currentCount;
```

Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Tanıtıcı prerendering

Prerendering sırasında:

* Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.
* Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.

`localStorage` veya `sessionStorage` prerendering sırasında kullanılamaz. Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.

Hatayı çözmek için bir yol prerendering devre dışı bırakılır. Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir. Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .

Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` . Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin. Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Durum korumasını ortak bir konuma ayırın

Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur. Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır. Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.

Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.

Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın. Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir. Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.

Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir. Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.

Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:

* Birçok bileşen genelinde durumu kullanmak için.
* Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.

Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.

::: moniker-end

::: zone-end
