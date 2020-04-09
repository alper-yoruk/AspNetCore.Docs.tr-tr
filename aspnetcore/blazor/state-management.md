---
title: ASP.NET Blazor Çekirdek devlet yönetimi
author: guardrex
description: Sunucu uygulamalarında Blazor durumu nasıl devam edersiniz öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218875"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Blazor Çekirdek devlet yönetimi

Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorSunucu, durum salkı bir uygulama çerçevesidir. Çoğu zaman, uygulama sunucuya sürekli bir bağlantı tutar. Kullanıcının durumu bir *devrede*sunucunun belleğinde tutulur. 

Bir kullanıcının devresi için tutulan duruma örnekler şunlardır:

* İşlenen UI&mdash;bileşen örnekleri hiyerarşisi ve bunların en son işlenme çıktısı.
* Bileşen örneklerindeki tüm alanların ve özelliklerin değerleri.
* Bağımlı [enjeksiyon (DI)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan ve devreye giren veriler.

> [!NOTE]
> Bu makale, Sunucu uygulamalarındaki Blazor durum kalıcılığını gidermelerini giderer. BlazorWebAssembly uygulamaları [tarayıcıda istemci tarafı durum kalıcılığından](#client-side-in-the-browser) yararlanabilir, ancak bu makalenin kapsamı dışında özel çözümler veya üçüncü taraf paketleri gerektirir.

## <a name="opno-locblazor-circuits"></a>BlazorDevre

Bir kullanıcı geçici bir ağ Blazor bağlantısı kaybı yla karşılanırsa, uygulamayı kullanmaya devam edebilmesi için kullanıcıyı özgün devresine yeniden bağlamaya çalışır. Ancak, bir kullanıcıyı sunucunun belleğindeki orijinal devresine yeniden bağlamak her zaman mümkün değildir:

* Sunucu bağlantısı kesilen bir devreyi sonsuza kadar tutamaz. Sunucu, bir zaman anından sonra veya sunucu bellek baskısı altındayken bağlantısı kesilmiş bir devre serbest bırakmalıdır.
* Çok sunuculu, yük dengeli dağıtım ortamlarında, sunucu işleme istekleri herhangi bir zamanda kullanılamaz hale gelebilir. Tek tek sunucular, isteklerin genel hacmini işlemek için artık gerek kıldığında başarısız olabilir veya otomatik olarak kaldırılabilir. Kullanıcı yeniden bağlanmaya çalıştığında özgün sunucu kullanılamayabilir.
* Kullanıcı tarayıcısını kapatıp yeniden açabilir veya tarayıcının belleğinde tutulan tüm durumları kaldıran sayfayı yeniden yükleyebilir. Örneğin, JavaScript interop aramaları aracılığıyla ayarlanan değerler kaybolur.

Bir kullanıcı özgün devresine yeniden bağlanadığında, kullanıcı boş bir durumda yeni bir devre alır. Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.

## <a name="preserve-state-across-circuits"></a>Devreler arasında durumu koruma

Bazı senaryolarda, devreler arasında durumu korumak arzu edilir. Bir uygulama, şu nedense, kullanıcı için önemli verileri saklayabilir:

* Web sunucusu kullanılamaz hale gelir.
* Kullanıcının tarayıcısı yeni bir web sunucusu ile yeni bir devre başlatmak zorunda kalır.

Genel olarak, devreler arasında durumu korumak, kullanıcıların yalnızca zaten var olan verileri okumak için değil, etkin olarak veri oluşturduğu senaryolar için de geçerlidir.

Durumu tek bir devrenin ötesinde korumak için, *verileri yalnızca sunucunun belleğinde depolamayın.* Uygulama, verileri başka bir depolama konumuna kadar devam ettirilmelidir. Durum kalıcılığı otomatik&mdash;değildir, durumu durumlu veri kalıcılığını uygulamak için uygulamayı geliştirirken adımlar atmalısınız.

Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturmak için çaba harcadığı yüksek değerli durum için gereklidir. Aşağıdaki örneklerde, kalıcı durum ya zaman kazandırır ya da ticari faaliyetlerde yardımcı olur:

* Çok adımlı &ndash; web formu Bir kullanıcının durumu kaybolursa, çok aşamalı bir işlemin tamamlanmış birkaç adımı için verileri yeniden girmek zaman alır. Kullanıcı, çok aşamalı formdan uzaklaşıp daha sonra forma dönerse bu senaryodaki durumunu kaybeder.
* Alışveriş &ndash; sepeti Potansiyel geliri temsil eden bir uygulamanın ticari açıdan önemli herhangi bir bileşeni korunabilir. Durumunu ve dolayısıyla alışveriş sepetini kaybeden bir kullanıcı, siteye daha sonra döndüklerinde daha az ürün veya hizmet satın alabilir.

Genellikle, gönderilmemiş bir oturum açma iletişim kutusuna girilen kullanıcı adı gibi, kolayca yeniden oluşturulan durumu korumak gerekmez.

> [!IMPORTANT]
> Bir uygulama yalnızca *uygulama durumunu*devam ettir. UI'ler, bileşen örnekleri ve bunların oluşturma ağaçları gibi kalıcı olamaz. Bileşenler ve render ağaçları genellikle serileştirilebilir değildir. Kullanıcı Arabirimi durumuna benzer bir şeyi (TreeView'in genişletilmiş düğümleri gibi) devam ettirebilmek için, uygulamanın davranışı serileştirilebilir uygulama durumu olarak modellemek için özel kodu olması gerekir.

## <a name="where-to-persist-state"></a>Nerede durum devam etmek

Bir Blazor Server uygulamasında kalıcı durum için üç ortak konum vardır. Her yaklaşım en iyi farklı senaryolar için uygundur ve farklı uyarılar vardır:

* [Veritabanında sunucu tarafı](#server-side-in-a-database)
* [URL](#url)
* [Tarayıcıda istemci tarafı](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Veritabanında sunucu tarafı

Kalıcı veri kalıcılığı veya birden çok kullanıcı veya aygıta yayılması gereken veriler için, bağımsız bir sunucu tarafı veritabanı neredeyse kesinlikle en iyi seçimdir. Seçeneklere şunlar dahildir:

* İlişkisel SQL veritabanı
* Anahtar değeri deposu
* Blob mağazası
* Tablo deposu

Veriler veritabanına kaydedildikten sonra, yeni bir devre herhangi bir zamanda bir kullanıcı tarafından başlatılabilir. Kullanıcının verileri korunur ve herhangi bir yeni devrede kullanılabilir.

Azure veri depolama seçenekleri hakkında daha fazla bilgi için [Azure Depolama Belgeleri](/azure/storage/) ve Azure [Veritabanları'na](https://azure.microsoft.com/product-categories/databases/)bakın.

### <a name="url"></a>URL'si

Gezinme durumunu temsil eden geçici veriler için, verileri URL'nin bir parçası olarak modellendirin. URL'de modellenen durum örnekleri şunlardır:

* Görüntülenen varlığın kimliği.
* Sayfalı kılavuzdaki geçerli sayfa numarası.

Tarayıcının adres çubuğunun içeriği korunur:

* Kullanıcı sayfayı el ile yeniden yüklerse.
* Web sunucusu kullanılamaz&mdash;hale gelirse, kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemek zorunda kalır.

`@page` Yönergeyle URL desenlerini tanımlama hakkında <xref:blazor/routing>bilgi için bkz.

### <a name="client-side-in-the-browser"></a>Tarayıcıda istemci tarafı

Kullanıcının etkin olarak oluşturduğu geçici veriler için, tarayıcının `localStorage` ve `sessionStorage` koleksiyonların ortak bir destek deposudur. Uygulamanın, devre terk edilmişse depolanan durumu yönetmesi veya temizlemesi gerekmez, bu da sunucu tarafındaki depolamaya göre bir avantajdır.

> [!NOTE]
> Bu bölümdeki "istemci tarafı" tarayıcıdaki istemci tarafı senaryolarını ifade eder, [ Blazor WebAssembly barındırma modeline](xref:blazor/hosting-models#blazor-webassembly)değil. `localStorage`ve `sessionStorage` WebAssembly Blazor uygulamalarında ancak özel kod yazarak veya üçüncü taraf paketi kullanılarak kullanılabilir.

`localStorage`ve `sessionStorage` aşağıdaki gibi farklıdır:

* `localStorage`kullanıcının tarayıcısına kapsamlıdır. Kullanıcı sayfayı yeniden yüklerse veya tarayıcıyı kapatıp yeniden açarsa, durum devam eder. Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır. Veriler açıkça `localStorage` temizlenene kadar devam eder.
* `sessionStorage`kullanıcının tarayıcı sekmesine göre dir. Kullanıcı sekmeyi yeniden yüklerse, durum devam eder. Kullanıcı sekmeyi veya tarayıcıyı kapatırsa, durum kaybolur. Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekme de verilerin kendi bağımsız sürümüne sahiptir.

Genellikle, `sessionStorage` kullanımı daha güvenlidir. `sessionStorage`bir kullanıcının birden çok sekme açması ve aşağıdakilerle karşılaşma sı riski nden kaçınır:

* Sekmeler arasında devlet depolama hataları.
* Sekme diğer sekmelerin durumunun üzerine yazdığında kafa karıştırıcı davranış.

`localStorage`uygulamanın tarayıcıyı kapatma ve yeniden açma durumunda durum devam etmesi gerekiyorsa daha iyi bir seçimdir.

Tarayıcı depolama alanı kullanmak için uyarılar:

* Sunucu tarafındaki veritabanıkullanımına benzer şekilde, veri yükleme ve kaydetme eşzamanlıdır.
* Sunucu tarafındaki veritabanının aksine, istenen sayfa ön oluşturma aşamasında tarayıcıda bulunmadığından, ön işleme sırasında depolama alanı kullanılamaz.
* Birkaç kilobaytlık verinin depolanması, Sunucu Blazor uygulamaları için kalıcı olması mantıklıdır. Birkaç kilobayt ötesinde, veriler ağ üzerinden yüklendiğinden ve kaydedildiklerinden performans etkilerini göz önünde bulundurmanız gerekir.
* Kullanıcılar verileri görüntüleyebilir veya kurcalayabilir. ASP.NET Çekirdek [Veri Koruması](xref:security/data-protection/introduction) riski azaltabilir.

## <a name="third-party-browser-storage-solutions"></a>Üçüncü taraf tarayıcı depolama çözümleri

Üçüncü taraf NuGet paketleri ile `localStorage` çalışmak `sessionStorage`için API'ler sağlar ve.

Core'un [Veri Koruması'nı](xref:security/data-protection/introduction)saydam olarak kullanan bir paketi ASP.NET dikkate almaya değer. ASP.NET Çekirdek Veri Koruması depolanan verileri şifreler ve depolanan verilerle oynama riskini azaltır. JSON serileştirilmiş veriler düz metinolarak depolanırsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri değiştirebilir. Veriler doğada önemsiz olabileceğinden, verileri güvence altına almak her zaman sorun değildir. Örneğin, kullanıcı arabirimi öğesinin depolanan rengini okumak veya değiştirmek kullanıcı veya kuruluş için önemli bir güvenlik riski değildir. Kullanıcıların *hassas verileri*incelemesine veya kurcalanmasına izin vermekten kaçının.

## <a name="protected-browser-storage-experimental-package"></a>Korumalı Tarayıcı Depolama deneysel paketi

[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)için `localStorage` `sessionStorage` [Veri Koruması](xref:security/data-protection/introduction) sağlayan nuget paketinin bir örneğidir.

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`şu anda üretim için uygun olmayan desteklenmeyen bir deneysel pakettir.

### <a name="installation"></a>Yükleme

Paketi yüklemek `Microsoft.AspNetCore.ProtectedBrowserStorage` için:

1. Server Blazor uygulama projesinde, [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)bir paket başvuru ekleyin.
1. Üst düzey HTML'de (örneğin, varsayılan proje şablonundaki *Pages/_Host.cshtml* dosyasında) aşağıdaki `<script>` etiketi ekleyin:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. `Startup.ConfigureServices` Yöntemde, hizmet `AddProtectedBrowserStorage` koleksiyonuna ekleme `localStorage` çağrısı ve `sessionStorage` hizmetleri:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Bir bileşen içinde veri kaydetme ve yükleme

Tarayıcı depolamasına veri yüklemeyi veya kaydetmeyi [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) gerektiren herhangi bir bileşende, aşağıdakilerden birini enjekte etmek için kullanın:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Seçim, hangi destek mağazasını kullanmak istediğinize bağlıdır. Aşağıdaki örnekte `sessionStorage` kullanılır:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

İfade, `@using` bileşen yerine *_Imports.razor* dosyasına yerleştirilebilir. *_Imports.razor* dosyasının kullanımı, ad alanını uygulamanın daha büyük kesimleri veya uygulamanın tüm bölümleri için kullanılabilir hale getirir.

Proje `_currentCount` şablonunun `Counter` bileşenindeki değeri sürdürmek için, `IncrementCount` kullanılacak `ProtectedSessionStore.SetAsync`yöntemi değiştirin:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Daha büyük, daha gerçekçi uygulamalarda, tek tek alanların depolanması olası bir senaryodur. Uygulamaların karmaşık durum içeren tüm model nesnelerini depolama olasılığı daha yüksektir. `ProtectedSessionStore`json verilerini otomatik olarak seri hale eder ve deserialize eder.

Önceki kod örneğinde, `_currentCount` veriler kullanıcının `sessionStorage['count']` tarayıcısında olduğu gibi depolanır. Veriler düz metinolarak depolanır, daha çok ASP.NET Core's [Data Protection](xref:security/data-protection/introduction)kullanılarak korunur. Şifrelenmiş veriler tarayıcının `sessionStorage['count']` geliştirici konsolunda değerlendirilip değerlendirilip değerlendirildiği görülebilir.

Kullanıcı `_currentCount` `Counter` daha sonra bileşene dönerse (tamamen yeni bir devredeyse dahil) verileri `ProtectedSessionStore.GetAsync`kurtarmak için şunları kullanın:

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Bileşenin parametreleri navigasyon durumunu içeriyorsa, sonucu `OnParametersSetAsync`değil, `OnInitializedAsync`' da çağırın `ProtectedSessionStore.GetAsync` ve atayın. `OnInitializedAsync`bileşen ilk anlık olduğunda yalnızca bir kez çağrılır. `OnInitializedAsync`kullanıcı aynı sayfada kalırken farklı bir URL'ye giderse daha sonra tekrar çağrılmaz. Daha fazla bilgi için bkz. <xref:blazor/lifecycle>.

> [!WARNING]
> Bu bölümdeki örnekler yalnızca sunucu önceden oluşturma etkin değilse çalışır. Ön işleme etkin olduğunda, şuna benzer bir hata oluşturulur:
>
> > JavaScript interop aramaları şu anda düzenlenemez. Bunun nedeni, bileşenin önceden işlenmiş olmasıdır.
>
> Ön oluşturmayı devre dışı kılabilir veya ön oluşturmayla çalışmak için ek kod ekleyin. Ön oluşturmayla çalışan kod yazma hakkında daha fazla bilgi edinmek için [Tutvet ön oluşturma](#handle-prerendering) bölümüne bakın.

### <a name="handle-the-loading-state"></a>Yükleme durumunu işleme

Tarayıcı depolama asynchronous (bir ağ bağlantısı üzerinden erişilen) olduğundan, verilerin yüklenmesi ve bir bileşen tarafından kullanılabilir önce her zaman bir süre vardır. En iyi sonuçlar için, boş veya varsayılan verileri görüntülemek yerine yükleme devam ederken bir yükleme durumu iletisi oluşturma.

Bir yaklaşım, verilerin `null` (hala yüklenip yüklenmediğini) izlemektir. Varsayılan `Counter` bileşende, sayım bir `int`. Türe soru işareti ( `_currentCount` `?`) ekleyerek nullable olun (`int`):

```csharp
private int? _currentCount;
```

Sayım ve **Artış** düğmesini koşulsuz görüntülemek yerine, bu öğeleri yalnızca veriler yüklendiğinde görüntülemeyi seçin:

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Ön işleme yi işle

Ön işleme sırasında:

* Kullanıcının tarayıcısına etkileşimli bağlantı yok.
* Tarayıcıda henüz JavaScript kodunu çalıştırabileceği bir sayfa yok.

`localStorage`veya `sessionStorage` ön işleme sırasında kullanılamaz. Bileşen depolama yla etkileşime girmezse, aşağıdakilere benzer bir hata oluşturulur:

> JavaScript interop aramaları şu anda düzenlenemez. Bunun nedeni, bileşenin önceden işlenmiş olmasıdır.

Hatayı gidermenin bir yolu, ön oluşturmayı devre dışı bilebilir. Uygulama tarayıcı tabanlı depolamayı yoğun olarak kullanıyorsa, bu genellikle en iyi seçimdir. Ön oluşturma karmaşıklık ekler ve uygulama kullanılabilir olana kadar `localStorage` `sessionStorage` yararlı bir içeriği önceden işleyemediği için uygulamadan yarar lanamaz.

Ön oluşturmayı devre dışı kardırmak için `render-mode` *Pages/_Host.cshtml* dosyasını açTırım ve Bileşen Etiket <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>Yardımcısını ' nın ' ı ' nı değiştir [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)

Ön oluşturma, kullanmayan `localStorage` veya `sessionStorage`başka sayfalar için yararlı olabilir. Ön işlemeyi etkin tutmak için, yükleme işlemini tarayıcı devreye bağlanana kadar erteleyin. Aşağıdaki sayaç değeri depolamak için bir örnektir:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Devlet korumasını ortak bir konuma faktör haline getirmek

Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcı kodunu birçok kez yeniden uygulamak kod yinelemesi oluşturur. Kod yinelemesini önlemek için bir seçenek, devlet sağlayıcı mantığı kapsülleyen bir *durum sağlayıcı üst bileşeni* oluşturmaktır. Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.

Aşağıdaki `CounterStateProvider` bileşen örneğinde, sayaç verileri kalıcıdır:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
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
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Bileşen, `CounterStateProvider` yükleme tamamlanana kadar alt içeriğini oluşturmayarak yükleme aşamasını işler.

`CounterStateProvider` Bileşeni kullanmak için, karşı duruma erişim gerektiren başka bir bileşenin etrafında bileşenin bir örneğini sarın. Durumu bir uygulamadaki tüm bileşenler için erişilebilir `CounterStateProvider` hale `Router` getirmek `App` için, bileşeni bileşenin etrafına sarın *(App.razor):*

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Sarılmış bileşenler kalıcı sayaç durumunu alır ve değiştirebilir. Aşağıdaki `Counter` bileşen deseni uygular:

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

Önceki bileşen ile `ProtectedBrowserStorage`etkileşim için gerekli değildir , ne de bir "yükleme" aşaması ile anlaşma yok.

Daha önce açıklandığı gibi ön `CounterStateProvider` işleme ile başa çıkmak için, sayaç verilerini tüketen tüm bileşenlerin otomatik olarak önişleme ile çalışması için değiştirilebilir. Ayrıntılar için [Tutamaç ön oluşturma](#handle-prerendering) bölümüne bakın.

Genel olarak, *durum sağlayıcı üst bileşen* deseni önerilir:

* Diğer birçok bileşende durum tüketmek için.
* Eğer devam etmek için sadece bir üst düzey durum nesnesi varsa.

Birçok farklı durum nesnesini devam ettirmek ve farklı yerlerdefarklı nesne alt kümelerini tüketmek için, genel olarak devletin yüklenmesi ve kaydedilmesinden kaçınmak daha iyidir.
