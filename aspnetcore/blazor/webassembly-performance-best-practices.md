---
title: ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri
author: pranavkm
description: ASP.NET Core uygulamalarında performansı artırma Blazor WebAssembly ve sık karşılaşılan performans sorunlarından kaçınmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 423745d734d8da2b8f3f974f9b4dd1a0265d4877
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054742"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri

, [Pranav Krishnamoorthy](https://github.com/pranavkm) ve [Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Blazor WebAssembly , en gerçekçi uygulama kullanıcı arabirimi senaryolarında yüksek performansa olanak tanımak için dikkatle tasarlanır ve iyileştirilir. Ancak en iyi sonuçları üretmek, doğru desenleri ve özellikleri kullanan geliştiricilere bağlıdır. Aşağıdaki noktaları göz önünde bulundurun:

* **Çalışma zamanı işleme** : .NET kodu WebAssembly çalışma zamanının içindeki yorumlayıcı üzerinde çalışır, bu nedenle CPU üretilen işi sınırlıdır. Yoğun senaryolarda uygulama, [işleme hızını iyileştirmenin](#optimize-rendering-speed)avantajlarından yararlanır.
* **Başlangıç zamanı** : uygulama bir .NET çalışma zamanını tarayıcıya aktarır, bu nedenle [uygulama indirme boyutunu en aza indirecek](#minimize-app-download-size)Özellikler kullanılması önemlidir.

## <a name="optimize-rendering-speed"></a>İşleme hızını iyileştirme

Aşağıdaki bölümler, işleme iş yükünü en aza indirmek ve UI yanıt hızını artırmak için öneriler sağlar. Bu tavsiyeler, UI işleme hızlarındaki *on katlamalı veya daha yüksek bir geliştirmeyi* kolayca yapabilir.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Bileşen alt ağaçlarını gereksiz şekilde işlemesini önleyin

Çalışma zamanında, bileşenler hiyerarşi olarak mevcuttur. Kök bileşenin alt bileşenleri vardır. Sırasıyla, kök alt bileşenleri kendi alt bileşenlerine sahiptir ve bu şekilde devam eder. Kullanıcı bir düğme seçen bir olay gerçekleştiğinde, bu, Blazor Hangi bileşenlere yeniden değer vermek için bu şekilde çalışır:

 1. Olayın kendisi, olayın işleyicisini işlenmiş olan bileşene gönderilir. Olay işleyicisini yürüttükten sonra, bu bileşen yeniden kullanılır.
 1. Herhangi bir bileşen yeniden her eklendiğinde, her bir alt bileşeninin parametre değerlerinin yeni bir kopyasını sağlar.
 1. Yeni bir parametre değerleri kümesi alınırken, her bileşen yeniden tekrar olup olmayacağını seçer. Varsayılan olarak, parametre değerleri değişmiş olabilir (örneğin, değişebilir nesnelerse), bileşenler yeniden işlenir.

Bu dizinin son iki adımı, bileşen hiyerarşisinde yinelemeli olarak devam eder. Çoğu durumda, tüm alt ağaç yeniden kullanılır. Bu, üst düzey bileşenleri hedefleyen olayların pahalı rerendering işlemlerine neden olabileceği anlamına gelir, çünkü bu noktanın altındaki her şey yeniden alınmalıdır.

Bu işlemi kesintiye uğratmak ve belirli bir alt ağaçta işleme özyinelemeyi engellemek istiyorsanız şunlardan birini yapabilirsiniz:

 * Belirli bir bileşene yönelik tüm parametrelerin temel sabit türlerde (örneğin,,,, `string` `int` `bool` `DateTime` ve diğerleri) olduğundan emin olun. Bu parametre değerlerinden hiçbiri değişmediği takdirde değişiklikleri tespit etmek için yerleşik mantık rerendering öğesini otomatik olarak atlar. Bir alt bileşeni ile oluşturduysanız `<Customer CustomerId="@item.CustomerId" />` , burada `CustomerId` bir değer olduğunda, `int` değişiklik ne zaman haricinde yeniden verilmez `item.CustomerId` .
 * Özel model türleri, olay geri çağırmaları veya değerler gibi temel olmayan parametre değerlerini kabul etmeniz gerekirse, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> oluşturma bölümünde açıklanan [ `ShouldRender` ](#use-of-shouldrender) kararı denetlemek için geçersiz kılabilirsiniz.

Tüm alt ağaçlar rerendering atlanarak, bir olay gerçekleştiğinde işleme maliyetinin büyük çoğunluğunu kaldırabilirsiniz.

UI 'nin bir parçası olan rerendering atlayabilmeniz için, özellikle alt bileşenleri çarpanlara ayırmak isteyebilirsiniz. Bu, bir üst bileşenin işleme maliyetini azaltmanın geçerli bir yoludur.

#### <a name="use-of-shouldrender"></a>Kullanım `ShouldRender`

Tek bir kullanıcı arabirimi yazıyorsanız (herhangi bir parametre değerinden bağımsız olarak) ilk işleme sonrasında hiçbir şekilde değişmez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` .

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Bileşen yalnızca parametre değerleri belirli yöntemlerle rerendering gerektiriyorsa, değişiklikleri algılamak üzere gerekli bilgileri izlemek için özel alanları kullanabilirsiniz. Aşağıdaki örnekte, yeniden `shouldRender` oluşturması gereken her türlü değişiklik veya mutasyon için denetimi temel alır. `prevOutboundFlightId` ve `prevInboundFlightId` sonraki olası güncelleştirme için bilgileri izleyin:

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

Önceki kodda, bir olay işleyicisi, `shouldRender` `true` bileşenin olaydan sonra yeniden bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde bir şekilde olarak da

Çoğu bileşen için bu düzeyde el ile denetim gerekli değildir. Yalnızca söz konusu alt ağaçlar işlemeye pahalı ise ve UI gecikmeye neden oluyorsa işleme alt ağaçlarını atlama konusunda endişelenmelisiniz.

Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Sanallaştırma

Bir döngüde büyük miktarlarda Kullanıcı arabirimini işlerken (örneğin, binlerce girişi olan bir liste veya kılavuz), işleme işlemlerinin birlikte işlenmesi, kullanıcı ARABIRIMI oluşturma işlemlerindeki bir gecikme süresine neden olabilir ve bu nedenle kötü bir kullanıcı deneyimi sağlar. Kullanıcının aynı anda yalnızca küçük sayıda öğeyi kaydırma yapmadan görebildiğinden, bu durumda, o anda görünür olmayan öğeleri çok fazla işlemek çok zaman harcayabilir.

Bunu çözmek için, rastgele Blazor büyük bir listenin Görünüm ve kaydırma davranışlarını oluşturan, ancak gerçekte yalnızca geçerli kaydırma görünüm penceresinin içindeki liste öğelerini işleyen yerleşik bir [ `<Virtualize>` bileşen](xref:blazor/components/virtualization) sağlar. Örneğin, bu, uygulamanın 100.000 girişi olan bir listesi olabileceği, ancak yalnızca herhangi bir zamanda görünür olan 20 öğenin işleme maliyetini ödeme anlamına gelir. Bileşenin kullanımı, `<Virtualize>` Kullanıcı arabirimi performansını büyüklük siparişlerine göre ölçeklendirebilir.

`<Virtualize>` Şu durumlarda kullanılabilir:

 * Bir döngüde veri öğeleri kümesi işleme.
 * Öğelerin çoğu kaydırma nedeniyle görünür değil.
 * İşlenen öğeler tam olarak aynı boyutta. Kullanıcı rastgele bir noktaya kaydığında, bileşen gösterilecek görünür öğeleri hesaplayabilir.

Aşağıda, sanallaştırılmış olmayan bir liste örneği gösterilmektedir:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

`allFlights`Koleksiyonda 10.000 öğe varsa, bu, 10.000 bileşen örneklerini oluşturur ve işler `<FlightSummary>` . Buna karşılık aşağıda, sanallaştırılmış bir liste örneği gösterilmektedir:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Sonuçta elde edilen kullanıcı ARABIRIMI bir kullanıcıya aynı olsa da, arka planda bileşen yalnızca başlatılır ve `<FlightSummary>` kaydırılabilir bölgeyi dolduracak kadar birçok örnek oluşturur. `<FlightSummary>`Görünen örnek kümesi, Kullanıcı kaydırıldığında yeniden hesaplanır ve işlenir.

`<Virtualize>` diğer avantajları da vardır. Örneğin, bir bileşen bir dış API 'den veri istediğinde, `<Virtualize>` bileşenin tüm verileri koleksiyondan indirmek yerine yalnızca geçerli görünür bölgeye karşılık gelen kayıt dilimini getirme izni verir.

Daha fazla bilgi için bkz. <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Hafif, iyileştirilmiş bileşenler oluşturma

Çoğu Blazor bileşen agresif iyileştirme çabalarına gerek kalmaz. Bunun nedeni çoğu bileşenin kullanıcı arabiriminde genellikle tekrarlanmamasından ve yüksek sıklıkta yeniden oturum etmamamasından kaynaklanır. Örneğin, `@page` iletişim kutuları veya formlar gibi üst düzey kullanıcı arabirimi parçalarını temsil eden bileşenler ve bileşenler, büyük olasılıkla yalnızca bir kez ve Kullanıcı hareketiyle yanıt olarak yalnızca bir kez görünür. Bu bileşenler yüksek bir işleme iş yükü oluşturmaz, bu sayede, işleme performansı hakkında endişelenmeden istediğiniz Framework özelliklerinin herhangi bir birleşimini ücretsiz olarak kullanabilirsiniz.

Ancak, ölçeklendirilmesi gereken bileşenleri oluşturduğunuz yaygın senaryolar da vardır. Örneğin:

 * Büyük iç içe yerleştirilmiş formlarda yüzlerce ayrı giriş, etiket ve diğer öğeler bulunabilir.
 * Kılavuzlarda binlerce hücre olabilir.
 * Dağılım çizimleri milyonlarca veri noktasına sahip olabilir.

Her birimi ayrı bileşen örnekleri olarak modelliyorsa, bunların çoğu, işleme performansının kritik hale gelebilmesini sağlayacak. Bu bölümde, Kullanıcı arabiriminin hızlı ve hızlı bir şekilde kalması için bu tür bileşenleri basit hale getirme önerileri sunulmaktadır.

#### <a name="avoid-thousands-of-component-instances"></a>Binlerce bileşen örneği kullanmaktan kaçının

Her bileşen, üst ve alt öğelerinden bağımsız olarak işleyebilen ayrı bir adadır. Kullanıcı arabiriminin bir bileşen hiyerarşisine nasıl bölüneceği seçerek, Kullanıcı arabirimi işleme ayrıntı düzeyi üzerinde denetim elde edersiniz. Bu, performans için iyi veya hatalı olabilir.

 * Kullanıcı arabirimini daha fazla bileşene bölerek, olaylar gerçekleştiğinde UI 'nin daha küçük bir bölümünü kullanabilirsiniz. Örneğin, bir Kullanıcı bir tablo satırındaki bir düğmeye tıkladığında, tüm sayfa veya tablo yerine yalnızca bu tek satıra yeniden oturum açabiliyor olabilirsiniz.
 * Ancak, her ek bileşen bağımsız durum ve işleme yaşam döngüsü ile başa çıkmak için bazı ek bellek ve CPU ek yükü içerir.

Blazor WebAssembly.NET 5 ' te performansını ayarlamamız, bileşen örneği başına 0,06 MS 'nin bir işleme ek yükünü ölçyoruz. Bu, tipik bir dizüstü bilgisayarda çalışan üç parametre kabul eden basit bir bileşeni temel alır. Dahili olarak, ek yükün büyük ölçüde sözlüklerden bileşen başına durumunun alınması ve parametrelerin geçirilmesi ve alınması gerekir. Çarpma ile, 2.000 ek bileşen örneği eklemenin işleme zamanına 0,12 saniye ekleymesinin ve Kullanıcı arabiriminin kullanıcılara ağır bir şekilde yavaşladığı hakkında bilgi alabilirsiniz.

Daha fazlasına sahip olabilmeleri için bileşenleri daha hafif hale getirmek mümkündür, ancak genellikle daha güçlü bir teknik çok sayıda bileşene sahip olmaz. Aşağıdaki bölümlerde iki yaklaşım açıklanır.

##### <a name="inline-child-components-into-their-parents"></a>Satır içi alt bileşenleri üst öğeleri

Alt bileşenlerden oluşan bir diziyi işleyen aşağıdaki bileşeni göz önünde bulundurun:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Yukarıdaki örnek kod için bileşen, şunu `<ChatMessageDisplay>` içeren bir dosyada tanımlanmıştır `ChatMessageDisplay.razor` :

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

Yukarıdaki örnek iyi çalışır ve binlerce ileti bir kerede gösterilmemektedir. Tek seferde binlerce iletiyi göstermek için ayrı bileşeni düzenleme *etmeyi* göz önünde bulundurun `ChatMessageDisplay` . Bunun yerine, doğrudan üst öğe içinde işleme satır içi:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

Bu, çok sayıda alt bileşeni, her birinin bağımsız olarak yeniden yönlendirilmemesini sağlayacak şekilde işleme için bileşen başına ek yükünü önler.

##### <a name="define-reusable-renderfragments-in-code"></a>Kodda yeniden kullanılabilir `RenderFragments` olarak tanımlayın

Yalnızca işleme mantığını yeniden kullanma gibi alt bileşenler düzenleme olabilirsiniz. Bu durumda, gerçek bileşenleri bildirmeden işleme mantığını yeniden kullanmak yine de mümkündür. Herhangi `@code` bir bileşenin bloğunda, <xref:Microsoft.AspNetCore.Components.RenderFragment> Kullanıcı arabirimini yayar ve her yerden çağrılabilecek bir ' i tanımlayabilirsiniz:

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Yukarıdaki örnekte belirtildiği gibi, bileşenler bloğunun içindeki ve dışındaki bir kodu işaret edebilir `@code` . Bu yaklaşım <xref:Microsoft.AspNetCore.Components.RenderFragment> , isteğe bağlı olarak birden çok yerde, bileşenin normal işleme çıkışı içinde işleyebilmeniz gereken bir temsilciyi tanımlar. `__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> Razor Derleyicinin kendisi için işleme talimatları üretebilmesi için tür adlı bir parametreyi kabul etmesi gerekir.

Bu, birden çok bileşen arasında yeniden kullanılabilir hale getirmek istiyorsanız, onu üye olarak bildirmeyi göz önünde bulundurun `public static` :

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Bu, artık ilişkisiz bir bileşenden çağrılabilir. Bu teknik, bileşen başına ek yük olmadan işlenen yeniden kullanılabilir biçimlendirme kod parçacıklarının kitaplıklarını oluşturmak için faydalıdır.

<xref:Microsoft.AspNetCore.Components.RenderFragment> Temsilciler Ayrıca parametreleri kabul edebilir. Önceki örnekteki bileşenin eşdeğerini oluşturmak için `ChatMessageDisplay` :

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Bu yaklaşım, bileşen başına ek yük olmadan işleme mantığını yeniden kullanma avantajını sağlar. Ancak, Kullanıcı arabiriminin alt ağacını bağımsız olarak yenileyebilme avantajına sahip değildir veya bileşen sınırı olmadığından, üst öğesi oluştururken Kullanıcı arabiriminin alt ağacını işlemeyi atlayabilirsiniz.

#### <a name="dont-receive-too-many-parameters"></a>Çok fazla parametre almıyorsunuz

Bir bileşen, yüzlerce veya binlerce kez çok sık tekrarlarken, her bir parametreyi gönderme ve alma yükünün ne olduğunu göz önünde bulundurun.

Çok sayıda parametrenin performansı ciddi ölçüde kısıtladığından, ancak bir faktör olabilir. `<TableCell>`Bir kılavuz içinde 1.000 kez işleyen bir bileşen için, kendisine geçirilen her ek parametre, toplam işleme maliyetine 15 MS 'yi ekleyebilir. Her bir hücre 10 parametre kabul ettiğinde, parametre geçirme her bileşen işleme başına 150 MS 'yi, bu nedenle de 150.000 MS (150 saniye) ve kendi kendine bir bağımsız Kullanıcı arabirimine neden olur.

Bu yükü azaltmak için, özel sınıflar aracılığıyla birden çok parametreyi birlikte paketleyebilirsiniz. Örneğin, bir `<TableCell>` bileşen şunları kabul edebilir:

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

Yukarıdaki örnekte, `Data` her hücre için farklıdır, ancak `Options` tümünde ortaktır. Kuşkusuz, bir `<TableCell>` bileşenin olmaması ve bunun yerine ana bileşene ait mantığını satır içi olarak bir geliştirme olabilir.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Geçişli parametrelerin düzeltildiğinden emin olun

`<CascadingValue>`Bileşenin adlı isteğe bağlı bir parametresi vardır `IsFixed` .

 * `IsFixed`Değer `false` (varsayılan) ise, basamaklı değerin her alıcısı değişiklik bildirimlerini almak için bir abonelik ayarlar. Bu durumda, her biri, `[CascadingParameter]` abonelik izlemenin düzenli olarak **çok daha pahalıdır** `[Parameter]` .
 * `IsFixed`Değer `true` (örneğin, `<CascadingValue Value="@someValue" IsFixed="true">` ) ise, alıcı başlangıç değerini alır, ancak güncelleştirmeleri almak için herhangi bir abonelik ayarlamayın *not* . Bu durumda, her biri `[CascadingParameter]` hafif ve sıradan **daha pahalı** değildir `[Parameter]` .

Mümkün olan yerlerde, `IsFixed="true"` basamaklı değerler üzerinde kullanmanız gerekir. Bunu, sağlanan değer zaman içinde değişmeyen her seferinde yapabilirsiniz. Bir bileşenin `this` basamaklı bir değer olarak geçtiği ortak düzende şunu kullanmanız gerekir `IsFixed="true"` :

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Bu, basamaklı değeri alan çok sayıda bileşen varsa büyük bir farklılık yapar. Daha fazla bilgi için bkz. <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>İle öznitelik azaltma `CaptureUnmatchedValues`

Bileşenler, bayrağını kullanarak "eşleşmeyen" parametre değerleri almayı seçebilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> :

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Bu yaklaşım, öğesine rastgele ek özniteliklerin geçirilmesini sağlar. Ancak, oluşturucunun şunları gerektirdiğinden de oldukça pahalıdır:

* Bir sözlük oluşturmak için, sağlanan parametrelerin tümünü bilinen parametreler kümesiyle eşleştirin.
* Aynı özniteliğin birden çok kopyasının birbirinin üzerine yazılmasına devam edin.

<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Performans açısından kritik olmayan bileşenlere (çoğunlukla yinelenmeyen olanlar gibi) ücretsiz olarak yararlanabilirsiniz. Ancak, bir kılavuzdaki büyük bir liste veya hücrelerde bulunan her öğe gibi ölçekli bir şekilde işlenen bileşenler için, öznitelik slatmasını kullanmaktan kaçının.

Daha fazla bilgi için bkz. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>`SetParametersAsync`El ile uygulama

Bileşen başına işleme ek yükünün ana yönlerinden biri, özelliklere gelen parametre değerlerini yazıyor `[Parameter]` . Bu işlemi yapmak için oluşturucunun yansıma kullanması vardır. Bu biraz iyileştirilse de, WebAssembly çalışma zamanında JıT desteğinin yokluğu sınırlar uygular.

Bazı olağanüstü durumlarda, yansıma kullanmaktan kaçınmak ve kendi parametre ayarı mantığınızı el ile uygulamak isteyebilirsiniz. Bu, şu durumlarda uygulanabilir:

 * Çok sık işleyen bir bileşeniniz var (örneğin, Kullanıcı arabiriminde yüzlerce veya binlerce kopyası vardır).
 * Birçok parametre kabul eder.
 * Parametre alma yükünün Kullanıcı arabirimi yanıtlama hızı üzerinde bir observable etkisi olduğunu fark edersiniz.

Bu durumlarda, bileşenin sanal yöntemini geçersiz kılabilir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ve kendi bileşenine özgü mantığınızı uygulayabilirsiniz. Aşağıdaki örnek, tüm sözlük aramalarını kasıtlı olarak engeller:

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

Yukarıdaki kodda, temel sınıfı döndürmek, <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> parametreleri yeniden atamadan normal yaşam döngüsü yöntemlerini çalıştırır.

Yukarıdaki kodda görebileceğiniz gibi, <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> özel mantığı geçersiz kılma ve sağlama, karmaşık ve laborious, bu nedenle bu yaklaşım genel olarak önerilmez. Olağanüstü durumlarda, işleme performansını% 20-25 oranında iyileştirebilir, ancak bu yaklaşımı yalnızca daha önce listelenen senaryolarda düşünmeniz gerekir.

### <a name="dont-trigger-events-too-rapidly"></a>Olayları çok hızlı tetiklemez

Bazı tarayıcı olayları son derece sıklıkla `onmousemove` (örneğin, saniyede) çok sık harekete geçmektedir `onscroll` . Çoğu durumda, bu sıklıkla Kullanıcı Arabirimi güncelleştirmeleri gerçekleştirmeniz gerekmez. Bunu yapmayı denerseniz, Kullanıcı Arabirimi yanıt verme veya aşırı CPU süresi tükettiğiniz için zararlı olabilirsiniz.

Yerel `@onmousemove` veya olayları kullanmak yerine `@onscroll` , daha az sıklıkta başlatılan bir geri aramayı kaydetmek için js birlikte çalışabilirliği kullanmayı tercih edebilirsiniz. Örneğin, aşağıdaki bileşen ( `MyComponent.razor` ) fare konumunu, ancak her 500 MS için yalnızca en fazla güncelleştirme olduğunu gösterir:

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

Sayfaya yerleştirilebilecek `index.html` veya BIR ES6 modülü olarak yüklenen Ilgili JavaScript kodu, gerçek DOM olay dinleyicisini kaydeder. Bu örnekte, olay dinleyicisi, çağırma oranını sınırlamak için [Lodash `throttle` işlevini](https://lodash.com/docs/4.17.15#throttle) kullanır:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Blazor ServerHer olay çağrısı ağ üzerinden bir ileti teslimi içerdiğinden, bu teknik daha da önemli olabilir. Blazor WebAssemblyİşleme işinin miktarını önemli ölçüde azaltabildiğinden, için bu önemlidir.

## <a name="optimize-javascript-interop-speed"></a>JavaScript birlikte çalışma hızını iyileştirme

.NET ve JavaScript arasındaki çağrılar bazı ek yük içerir, çünkü:

 * Varsayılan olarak, çağrılar zaman uyumsuzdur.
 * Varsayılan olarak, parametreler ve dönüş değerleri JSON serileştirilir. Bu, .NET ve JavaScript türleri arasında kolay anlaşılır bir dönüştürme mekanizması sağlamaktır.

Ayrıca Blazor Server , bu çağrılar ağ üzerinden geçirilir.

### <a name="avoid-excessively-fine-grained-calls"></a>Aşırı hassas çağrılardan kaçının

Her çağrı bazı ek yük içerdiğinden, çağrı sayısını azaltmak faydalı olabilir. Tarayıcı deposunda bir öğe koleksiyonunu depolayan aşağıdaki kodu göz önünde bulundurun `localStorage` :

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

Yukarıdaki örnek her öğe için ayrı bir JS birlikte çalışma çağrısı yapar. Bunun yerine, aşağıdaki yaklaşım, JS birlikte çalışabilirliğine tek bir çağrı azaltır:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

Karşılık gelen JavaScript işlevi aşağıdaki gibi tanımlanır:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

İçin Blazor WebAssembly , bu genellikle çok SAYıDA js birlikte çalışma çağrısı yapıyorsanız önemlidir.

### <a name="consider-making-synchronous-calls"></a>Zaman uyumlu çağrılar yapmayı düşünün

Çağrılan kodun zaman uyumlu veya zaman uyumsuz olmasına bakılmaksızın, JavaScript birlikte çalışma çağrıları varsayılan olarak zaman uyumsuzdur. Bu, bileşenlerin hem hem de ile uyumlu olmasını Blazor WebAssembly sağlamaktır Blazor Server . Üzerinde Blazor Server , bir ağ bağlantısı üzerinden gönderildiğinden tüm JavaScript birlikte çalışma çağrılarının zaman uyumsuz olması gerekir.

Uygulamanızın yalnızca üzerinde çalıştırıldıklarından emin değilseniz Blazor WebAssembly , zaman uyumlu JavaScript birlikte çalışma çağrıları yapmayı tercih edebilirsiniz. Bu, zaman uyumsuz çağrılar yapmaktan biraz daha az yüke sahiptir ve sonuçları beklerken ara durum olmadığından daha az işleme döngülerine neden olabilir.

.NET 'ten JavaScript 'e zaman uyumlu bir çağrı yapmak için şu noktaya <xref:Microsoft.JSInterop.IJSRuntime> geçin <xref:Microsoft.JSInterop.IJSInProcessRuntime> :

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

İle çalışırken `IJSObjectReference` , ' ye atama yaparak zaman uyumlu bir çağrı yapabilirsiniz `IJSInProcessObjectReference` .

::: moniker-end

JavaScript 'ten .NET 'e zaman uyumlu bir çağrı yapmak için `DotNet.invokeMethod` yerine kullanın `DotNet.invokeMethodAsync` .

Zaman uyumlu çağrılar şu durumlarda çalışır:

* Uygulama üzerinde çalışıyor Blazor WebAssembly , değil Blazor Server .
* Çağrılan işlev, zaman uyumlu bir değer döndürür (bir `async` yöntem değildir ve bir .net <xref:System.Threading.Tasks.Task> veya JavaScript döndürmez `Promise` ).

Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Sıradan olmayan çağrılar yapmayı düşünün

Üzerinde çalışırken Blazor WebAssembly , .net 'Ten JavaScript 'e sıradan geri çağrı yapmak mümkündür. Bunlar bağımsız değişkenlerin JSON serileştirmesini veya dönüş değerlerini gerçekleştirmemenizi zaman uyumlu çağrılardır. Bellek yönetiminin ve .NET ve JavaScript temsilleri arasındaki çevirilerin her yönü, geliştiriciye kalmadı.

> [!WARNING]
> Kullanırken `IJSUnmarshalledRuntime` , JS birlikte çalışma yaklaşımının en az ek yükü olsa da, bu API 'lerle etkileşimde bulunmak için gereken JavaScript API 'leri Şu anda açıklanmamıştır ve gelecekteki sürümlerde yapılan değişikliklere tabidir.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Uygulama indirme boyutunu en aza indir

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Ara dil (IL) kırpma

[Kullanılmayan derlemelerin bir Blazor WebAssembly uygulamadan bölünmesi](xref:blazor/host-and-deploy/configure-trimmer) , uygulamanın ikili dosyalarında kullanılmayan kodu kaldırarak uygulamanın boyutunu küçültür. Varsayılan olarak, bir uygulama yayımlanırken ayarlayıcısı yürütülür. Kırpma özelliğinden faydalanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komutunu kullanarak [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan uygulamayı dağıtım için yayımlayın `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Ara dil (IL) bağlama

Uygulamanın [bağlanması Blazor WebAssembly ](xref:blazor/host-and-deploy/configure-linker) , uygulamanın ikili dosyalarında kullanılmayan kodu kırparak uygulamanın boyutunu azaltır. Varsayılan olarak, ara dil (IL) bağlayıcı yalnızca yapılandırmada oluşturulurken etkindir `Release` . Bu özellikten yararlanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan komutunu kullanarak uygulamayı dağıtım için yayımlayın `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Üzerinde System.Text.Jskullan

Blazor' nin JS birlikte çalışma uygulamasının <xref:System.Text.Json> , düşük bellek ayırması olan yüksek performanslı BIR JSON serileştirme kitaplığı olan ' i kullanır. Kullanmak <xref:System.Text.Json> , bir veya daha fazla ALTERNATIF JSON kitaplığı ekleyerek ek uygulama yükü boyutuna neden olmaz.

Geçiş Kılavuzu için, bkz. ' [den `Newtonsoft.Json` `System.Text.Json` ' ye geçiş ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Yavaş yük derlemeleri

Derlemeler bir rota için gerektiğinde çalışma zamanında derlemeleri yükleyin. Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Sıkıştırma

Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır. Blazor , içerik eklemek için sunucuyu kullanır ve statik olarak sıkıştırılan dosyaları sunar.

Bir uygulama dağıtıldıktan sonra uygulamanın sıkıştırılmış dosyalara hizmet ettiğini doğrulayın. Tarayıcının Geliştirici Araçları ağ sekmesini inceleyin ve dosyaların veya ile birlikte sunulduğunu doğrulayın `Content-Encoding: br` `Content-Encoding: gz` . Ana bilgisayar sıkıştırılmış dosyalara hizmet vermemişse, içindeki yönergeleri izleyin <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Kullanılmayan özellikleri devre dışı bırak

Blazor WebAssemblyçalışma zamanı, uygulamanın daha küçük bir yük boyutu için gerektirmemesi durumunda devre dışı bırakılabilen aşağıdaki .NET özelliklerini içerir:

* Saat dilimi bilgilerinin doğru olması için bir veri dosyası dahildir. Uygulama bu özelliği gerektirmiyorsa, `BlazorEnableTimeZoneSupport` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken Genelleştirme kaynaklarını taşır. Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan [sabit kültürü destekleyecek şekilde yapılandırabilirsiniz](xref:blazor/globalization-localization) `en-US` :

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Doğru çalışma gibi API 'Leri oluşturmak için harmanlama bilgileri eklenmiştir <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . Uygulamanın harmanlama verileri gerektirmediğinden emin değilseniz, `BlazorWebAssemblyPreserveCollationData` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
