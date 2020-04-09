---
title: ASP.NET Core Blazor uygulamalarındaki hataları işleme
author: guardrex
description: ASP.NET Blazor Core'un Blazor işlenmemiş özel durumları nasıl yönettiğini ve hataları algılayan ve işleyen uygulamaların nasıl geliştirilebildiğini keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382281"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>ASP.NET Core Blazor uygulamalarındaki hataları işleme

Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS)

Bu makalede, Blazor işlenmemiş özel durumları nasıl yönetildiği ve hataları algılayan ve işleyen uygulamaların nasıl geliştirilen anlatılmaktadır.

## <a name="detailed-errors-during-development"></a>Geliştirme sırasında ayrıntılı hatalar

Bir Blazor uygulama geliştirme sırasında düzgün çalışmadığında, uygulamadan ayrıntılı hata bilgileri almak sorun giderme ve sorunu gidermeye yardımcı olur. Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında bir altın çubuk görüntüler:

* Geliştirme sırasında, gold bar sizi özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.
* Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirer ve tarayıcının yenilenmesini önerir.

Bu hata işleme deneyimi için UI Blazor proje şablonlarının bir parçasıdır.

Bir Blazor WebAssembly uygulamasında, *wwwroot/index.html* dosyasındaki deneyimi özelleştirin:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Sunucu Blazor uygulamasında, *Pages/_Host.cshtml* dosyasındaki deneyimi özelleştirin:

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Öğe `blazor-error-ui` şablonlarda yer alan Blazor stiller tarafından gizlenir *(wwwroot/css/site.css)* ve bir hata oluştuğunda gösterilir:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Sunucu Blazor uygulaması işlenmemiş özel durumlara nasıl tepki verir?

BlazorSunucu durumsal bir çerçevedir. Kullanıcılar bir uygulamayla etkileşimde iken, *devre*olarak bilinen sunucuya bağlantı sağlarlar. Devre etkin bileşen örnekleri, artı durum, örneğin diğer birçok yönlerini tutar:

* Bileşenlerin en son işlenmiş çıktısı.
* İstemci tarafı olayları tarafından tetiklenebilen geçerli olay işleme temsilcileri kümesi.

Bir kullanıcı uygulamayı birden çok tarayıcı sekmesinde açarsa, birden çok bağımsız devresi vardır.

Blazorişlenmemiş çoğu özel durumu, oluştukları devre için ölümcül olarak ele alır. Bir devre işlenmemiş bir özel durum nedeniyle sonlandırılırsa, kullanıcı yalnızca yeni bir devre oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime devam edebilir. Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devreler olan sonlandırılan devrelerin dışındaki devreler etkilenmez. Bu senaryo, çöken uygulamayı çökerten bir masaüstü&mdash;uygulamasına benzer, ancak diğer uygulamalar etkilenmez.

İşlenmemiş bir özel durum aşağıdaki nedenlerle oluştuğunda bir devre sonlandırılır:

* İşlenmemiş bir özel durum genellikle devreyi tanımlanmamış bir durumda bırakır.
* Uygulamanın normal çalışması işlenmemiş bir özel durum sonrasında garanti edilemez.
* Devre devam ederse uygulamada güvenlik açıkları görünebilir.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Geliştirici kodunda işlenmemiş özel durumları yönetme

Bir uygulamanın hatadan sonra devam edeabilmesi için uygulamanın hata işleme mantığına sahip olması gerekir. Bu makalenin sonraki bölümleri, işlenmemiş özel durumların olası kaynaklarını açıklar.

Üretimde, oluşturma aracında çerçeve özel durum iletileri veya yığın izlemeleri oluşturmayın. Özel durum iletileri veya yığın izlemeleri oluşturma:

* Hassas bilgileri son kullanıcılara ifşa edin.
* Kötü niyetli bir kullanıcının uygulamanın, sunucunun veya ağın güvenliğini tehlikeye atabilecek bir uygulamadaki zayıflıkları keşfetmesinde yardımcı olun.

## <a name="log-errors-with-a-persistent-provider"></a>Kalıcı bir sağlayıcıyla hataları günlüğe kaydetme

İşlenmemiş bir özel durum oluşursa, <xref:Microsoft.Extensions.Logging.ILogger> özel durum hizmet kapsayıcısında yapılandırılan örneklere günlüğe kaydedilir. Varsayılan olarak, Blazor uygulamalar Konsol Günlük Sağlayıcısı ile çıktı konsolu için günlük. Günlük boyutunu ve günlük döndürmeyi yöneten bir sağlayıcıyla daha kalıcı bir konuma günlüğe kaydetmeyi düşünün. Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

Geliştirme sırasında, Blazor genellikle hata ayıklama yardımcı olmak için tarayıcının konsoluna özel durumların tüm ayrıntılarını gönderir. Üretimde, tarayıcının konsolundaki ayrıntılı hatalar varsayılan olarak devre dışı bırakılır, bu da hataların istemcilere gönderilmediği, ancak özel durum ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir. Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

Hangi olayların günlüğe kaydolacağına ve günlüğe kaydedilen olayların önem düzeyine karar vermelisiniz. Düşman kullanıcılar hataları kasıtlı olarak tetikleyebilir. Örneğin, ürün ayrıntılarını görüntüleyen bir bileşenin `ProductId` URL'sinde bilinmeyenin sağlandığı bir hatadan olay kaydetmeyin. Tüm hatalar günlüğe kaydetme için yüksek öneme yönelik olaylar olarak ele alınmamalıdır.

## <a name="places-where-errors-may-occur"></a>Hataların oluşabileceği yerler

Çerçeve ve uygulama kodu, aşağıdaki konumlardan herhangi birinde işlenmemiş özel durumları tetikleyebilir:

* [Bileşen anında](#component-instantiation)
* [Yaşam döngüsü yöntemleri](#lifecycle-methods)
* [Oluşturma mantığı](#rendering-logic)
* [Olay işleyicileri](#event-handlers)
* [Bileşen imha](#component-disposal)
* [JavaScript ile birlikte çalışma](#javascript-interop)
* [BlazorSunucu yeniden işleme](#blazor-server-prerendering)

Önceki işlenmemiş özel durumlar bu makalenin aşağıdaki bölümlerinde açıklanmıştır.

### <a name="component-instantiation"></a>Bileşen anında

Bir Blazor bileşen örneğini oluşturduğunda:

* Bileşenin oluşturucusu çağrılır.
* [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) Bileşenin oluşturucuya yönerge veya [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) öznitelik yoluyla sağlanan singleton olmayan DI hizmetlerinin üreticileri çağrılır.

Herhangi Blazor bir `[Inject]` özellik için çalıştırılan herhangi bir kurucu veya ayarlayıcı işlenmemiş bir özel durum attığında Sunucu devresi başarısız olur. Çerçeve bileşeni anında atamadığı için özel durum ölümcüldür. Oluşturucu mantığı özel durumlar oluşturabılırsa, uygulama hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalamalıdır.

### <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

Bir bileşenin ömrü Blazor boyunca, aşağıdaki [yaşam döngüsü yöntemlerini](xref:blazor/lifecycle)çağırır:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Herhangi bir yaşam döngüsü yöntemi eşzamanlı veya eşzamanlı olarak bir özel durum atarsa, özel durum bir Blazor Sunucu devresi için ölümcüldür. Bileşenlerin yaşam döngüsü yöntemlerindeki hatalarla başa çıkabilmek için hata işleme mantığı ekleyin.

Aşağıdaki örnekte, `OnParametersSetAsync` bir ürünü elde etmek için bir yöntem çağırır:

* `ProductRepository.GetProductByIdAsync` Yöntemde atılan bir özel durum `try-catch` bir deyim tarafından işlenir.
* `catch` Blok yürütüldüğünde:
  * `loadFailed`kullanıcıya `true`bir hata iletisi görüntülemek için kullanılan olarak ayarlanır.
  * Hata günlüğe kaydedilir.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Oluşturma mantığı

Bileşen dosyasındaki `.razor` bildirimbiçimlendirmecif olarak adlandırılan `BuildRenderTree`c# yönteminde derlenir. Bir bileşen, işlenen `BuildRenderTree` bileşenin öğelerini, metni ve alt bileşenlerini açıklayan bir veri yapısını işler, yürütür ve oluşturur.

Oluşturma mantığı bir özel durum atabilir. Bu senaryonun bir örneği `@someObject.PropertyName` değerlendirildiğinde `@someObject` oluşur, ancak . `null` Rendermantığı tarafından atılan işlenmemiş bir özel Blazor durum, bir Sunucu devresi için ölümcüldür.

Oluşturma mantığında geçersiz bir başvuru özel durum `null` larını önlemek için, üyelerine erişmeden önce bir nesneyi denetleyin. Aşağıdaki örnekte, `person.Address` aşağıdaki durumlarda özelliklere `person.Address` erişimez: `null`

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Önceki kod, `person` `null`'. Genellikle, kodun yapısı bileşenin işlendiği anda bir nesnenin var olduğunu garanti eder. Bu gibi durumlarda, oluşturma mantığı `null` için kontrol etmek gerekli değildir. Önceki örnekte, `person` bileşen anında oluşturulduğunda `person` oluşturulduğu için var olacağı garanti edilebilir.

### <a name="event-handlers"></a>Olay işleyicileri

İstemci tarafı kodu, olay işleyicileri kullanılarak oluşturulduğunda C# kodunun çağrılarını tetikler:

* `@onclick`
* `@onchange`
* Diğer `@on...` öznitelikler
* `@bind`

Olay işleyicisi kodu bu senaryolarda işlenmemiş bir özel durum atabilir.

Bir olay işleyicisi işlenmemiş bir özel durum atarsa (örneğin, bir Blazor veritabanı sorgusu başarısız olursa), özel durum bir Sunucu devresi için ölümcüldür. Uygulama dış nedenlerle başarısız olabilecek kodu çağırırsa, hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalayın.

Kullanıcı kodu özel durumu tutup işlemiyorsa, çerçeve özel durumu kaydeder ve devreyi sonlandırır.

### <a name="component-disposal"></a>Bileşen imha

Kullanıcı başka bir sayfada gezinme yaptığı için, örneğin bir bileşen Kullanıcı Arabirimi'nden kaldırılabilir. Uygulayan <xref:System.IDisposable?displayProperty=fullName> bir bileşen UI'den kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose*> yöntemini çağırır.

Bileşenin `Dispose` yöntemi işlenmemiş bir özel durum atarsa, özel Blazor durum bir Sunucu devresi için ölümcüldür. Elden çıkarma mantığı özel durumlar atayabı olabilirse, uygulama hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalamalıdır.

Bileşen imhası hakkında daha <xref:blazor/lifecycle#component-disposal-with-idisposable>fazla bilgi için bkz.

### <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

`IJSRuntime.InvokeAsync<T>`.NET kodunun kullanıcının tarayıcısında JavaScript çalışma süresine eşzamanlı arama lar yapmasına olanak tanır.

Aşağıdaki koşullar aşağıdaki `InvokeAsync<T>`ile hata işleme için geçerlidir:

* Bir çağrı `InvokeAsync<T>` eşzamanlı olarak başarısız olursa, bir .NET özel durum oluşur. Örneğin, `InvokeAsync<T>` sağlanan bağımsız değişkenler seri hale getirilemedığından, bir çağrı başarısız olabilir. Geliştirici kodu özel durumu yakalamak gerekir. Olay işleyicisi veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durum Blazor işlemiyorsa, ortaya çıkan özel durum bir Sunucu devresi için ölümcüldür.
* Bir çağrı `InvokeAsync<T>` eşzamanlı olarak başarısız olursa, <xref:System.Threading.Tasks.Task> .NET başarısız olur. Örneğin, `InvokeAsync<T>` JavaScript yan kodu bir özel durum attığından veya 'da `Promise` `rejected`tamamlanmış bir özel durum döndürür' den bir çağrı başarısız olabilir. Geliştirici kodu özel durumu yakalamak gerekir. [Bekleme](/dotnet/csharp/language-reference/keywords/await) işleci kullanıyorsanız, yöntem çağrısını hata işleme ve günlüğe kaydetme yle [deneme yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deyiminde sarmayı düşünün. Aksi takdirde, başarısız kod, bir Blazor Sunucu devresi için ölümcül olan işlenmemiş bir özel durumla sonuçlanır.
* Varsayılan olarak, `InvokeAsync<T>` çağrıların belirli bir süre içinde tamamlanması veya arama saatleri nin tamamlanması gerekir. Varsayılan zaman açıksırılım dönüşü Zaman açıkışığı kodu, tamamlanma iletisi geri gününü asla geri gününüolmayan ağbağlantısı veya JavaScript kodundaki bir kayba karşı korur. Arama zamanları dışarı çıkarsa, `Task` ortaya <xref:System.OperationCanceledException>çıkan bir . Bindirme ve günlük ile özel durum işleme.

Benzer şekilde, JavaScript kodu öznitelik tarafından [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) belirtilen .NET yöntemlerine çağrı lar başlatabilir. Bu .NET yöntemleri işlenmemiş bir özel durum atarsa:

* Özel durum, bir Blazor Sunucu devresi için ölümcül olarak kabul edilmez.
* JavaScript tarafı `Promise` reddedildi.

Yöntem çağrısının .NET tarafında veya JavaScript tarafında hata işleme kodunu kullanma seçeneğiniz vardır.

Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorSunucu önişleme

Blazor[bileşenleri,](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) kullanıcının ilk HTTP isteğinin bir parçası olarak işlenen HTML biçimlendirmesinin döndürülecek şekilde Bileşen Etiket Yardımcısı kullanılarak önceden oluşturulabilir. Bu çalışır:

* Aynı sayfanın parçası olan önceden işlenmiş bileşenlerin tümü için yeni bir devre oluşturma.
* İlk HTML oluşturma.
* Kullanıcının tarayıcısı `disconnected` aynı sunucuya bir SignalR bağlantı kurana kadar devreyi ele alır. Bağlantı kurulduğunda, devredeki etkileşim devam eder ve bileşenlerin HTML biçimlendirmesi güncelleştirilir.

Herhangi bir bileşen, örneğin, bir yaşam döngüsü yöntemi sırasında veya oluşturma mantığı nda, ön işleme sırasında işlenmemiş bir özel durum atarsa:

* Özel durum devre için ölümcüldür.
* Özel durum, Etiket Yardımcısı'ndan `Component` çağrı yığınına atılır. Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı sürece TÜM HTTP isteği başarısız olur.

Ön işleme başarısız olduğunda normal koşullar altında, çalışan bir bileşen oluşturulamadığından bileşeni oluşturmaya ve işlemeye devam etmek mantıklı değildir.

Ön işleme sırasında oluşabilecek hataları tolere etmek için, hata işleme mantığı özel durumlar atabilecek bir bileşenin içine yerleştirilmelidir. Hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimleri kullanın. Tag Helper'ı `Component` bir `try-catch` deyimde sarmak yerine, `Component` Tag Helper tarafından işlenen bileşene hata işleme mantığını yerleştirin.

## <a name="advanced-scenarios"></a>Gelişmiş senaryolar

### <a name="recursive-rendering"></a>Özyinelemeli görüntüleme

Bileşenler özyinelemeli olarak iç içe olabilir. Bu, özyinelemeli veri yapılarını temsil etmek için yararlıdır. Örneğin, bir `TreeNode` bileşen düğümün her bir çocuk için daha fazla `TreeNode` bileşen işleyebilir.

Özyinelemeli olarak işleme yaparken, sonsuz özyinelemeyle sonuçlanan kodlama desenlerinden kaçının:

* Döngü içeren bir veri yapısını özyinelemeli olarak işlemeyin. Örneğin, çocukları kendisini içeren bir ağaç düğümü oluşturmayın.
* Döngü içeren bir düzenler zinciri oluşturmayın. Örneğin, düzeni kendisi olan bir düzen oluşturmayın.
* Son kullanıcının kötü amaçlı veri girişi veya JavaScript interop çağrıları yoluyla özyineleme değişmezlerini (kuralları) ihlal etmesine izin vermeyin.

İşleme sırasında sonsuz döngüler:

* İşleme işleminin sonsuza kadar devam etmesine neden olur.
* Belirsiz bir döngü oluşturmaya eşdeğerdir.

Bu senaryolarda, etkilenen Blazor bir Sunucu devresi başarısız olur ve iş parçacığı genellikle aşağıdakileri yapmaya çalışır:

* İşletim sisteminin izin verdiği kadar CPU süresini süresiz olarak tüketin.
* Sınırsız miktarda sunucu belleği tüketin. Sınırsız bellek tüketmek, sonlanmamış bir döngünün her yinelemede bir koleksiyona girişler eklediği senaryoya eşdeğerdir.

Sonsuz özyineleme desenlerinden kaçınmak için, özyinelemeli işleme kodunun uygun durdurma koşulları içerdiğinden emin olun.

### <a name="custom-render-tree-logic"></a>Özel render ağacı mantığı

Bileşenlerin çoğu Blazor *.razor* dosyaları olarak uygulanır ve çıktılarını işlemek `RenderTreeBuilder` için a üzerinde çalışan mantık üretmek için derlenir. Bir geliştirici, yordamc# kodunu kullanarak mantığı el ile uygulayabilir. `RenderTreeBuilder` Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> El ile render ağacı oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmez gelişmiş ve güvenli olmayan bir senaryo olarak kabul edilir.

`RenderTreeBuilder` Kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir. Örneğin, geliştirici aşağıdakileri sağlamalıdır:

* Aramalar `OpenElement` ve `CloseElement` doğru dengelenir.
* Öznitelikler yalnızca doğru yerlerde eklenir.

Yanlış el ile işoluşturma ağacı oluşturucu mantığı, kilitlenmeler, sunucu askıları ve güvenlik açıkları gibi rasgele tanımlanmamış davranışlara neden olabilir.

El ile ağaç oluşturucu mantığını aynı karmaşıklık düzeyinde ve montaj kodu veya MSIL yönergelerini elle yazmakla aynı *tehlike* düzeyinde düşünün.
