---
title: ASP.NET Blazor Çekirdek yönlendirme
author: guardrex
description: Uygulamalardaki ve NavLink bileşeni yle ilgili istekleri nasıl yönlendireceklerini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218901"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Çekirdek Blazor yönlendirme

Yazar: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor uygulamalarında gezinme bağlantıları `NavLink` oluşturmak için istekleri nasıl yönlendireceklerini ve bileşeni nasıl kullanacağınızı öğrenin.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core uç nokta yönlendirme tümleştirmesi

Blazor [Server, Core Endpoint YönlendirmeASP.NET](xref:fundamentals/routing)entegre edilmiştir. Bir ASP.NET Core uygulaması, etkileşimli bileşenler için gelen `MapBlazorHub` `Startup.Configure`bağlantıları kabul etmek üzere yapılandırılmıştır:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

En tipik yapılandırma, tüm istekleri Blazor Server uygulamasının sunucu tarafındaki bölümünün ana bilgisayar ı olarak gören bir Razor sayfasına yönlendirmektir. Kural olarak, *ana sayfa* genellikle *_Host.cshtml*olarak adlandırılır. Ana bilgisayar dosyasında belirtilen rota, rota eşleştirmede düşük bir önceliğe sahip çalıştığından *geri dönüş rotası* olarak adlandırılır. Diğer rotalar eşleşmediğinde geri dönüş rotası dikkate alınr. Bu, uygulamanın Blazor Server uygulamasına müdahale etmeden diğer denetleyicileri ve sayfaları kullanmasına olanak tanır.

## <a name="route-templates"></a>Rota şablonları

Bileşen, `Router` belirtilen bir rota ile her bileşene yönlendirme sağlar. Bileşen `Router` *App.razor* dosyasında görünür:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Yönergesi olan `@page` bir *.razor* dosyası derlendiğinde, <xref:Microsoft.AspNetCore.Components.RouteAttribute> oluşturulan sınıfa rota şablonu belirten bir bilgi verilir.

Çalışma zamanında `RouteView` bileşen:

* `RouteData` İstenilen `Router` parametrelerle birlikte alır.
* Belirtilen bileşeni, belirtilen parametreleri kullanarak düzeniyle (veya isteğe bağlı varsayılan düzenle) işler.

İsteğe bağlı olarak, düzen belirtmeyen bileşenler için kullanılacak düzen sınıfı olan bir `DefaultLayout` parametre belirtebilirsiniz. Varsayılan Blazor şablonları `MainLayout` bileşeni belirtir. *MainLayout.razor* şablon projenin *Paylaşılan* klasöründedir. Düzenler hakkında daha fazla <xref:blazor/layouts>bilgi için bkz.

Bir bileşene birden çok rota şablonu uygulanabilir. Aşağıdaki bileşen isteklere `/BlazorRoute` yanıt `/DifferentBlazorRoute`verir ve:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL'lerin doğru şekilde çözümlemesi için `<base>` uygulamanın *wwwroot/index.html* dosyasına (Blazor WebAssembly) veya *Pages/_Host.cshtml* dosyasına (Blazor `href` Server)`<base href="/">`öznitelikte belirtilen uygulama temel yolu içeren bir etiket eklemesi gerekir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağlayın

Bileşen, `Router` istenen rota için içerik bulunamıyorsa uygulamanın özel içerik belirtmesine olanak tanır.

*App.razor* dosyasında, `NotFound` `Router` bileşenin şablon parametresinde özel içerik ayarlayın:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Etiketlerin `<NotFound>` içeriği, diğer etkileşimli bileşenler gibi rasgele öğeler içerebilir. İçerime varsayılan düzen `NotFound` uygulamak <xref:blazor/layouts>için bkz.

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

Bileşenin, `AdditionalAssemblies` routable bileşenlerini ararken göz önünde bulundurması gereken `Router` ek derlemeler belirtmek için parametreyi kullanın. Belirtilen `AppAssembly`derlemeler, belirtilen derlemeye ek olarak kabul edilir. Aşağıdaki örnekte, `Component1` başvurulan sınıf kitaplığında tanımlanan bir routable bileşenidir. Aşağıdaki `AdditionalAssemblies` örnek, aşağıdaki yönlendirme desteğiile `Component1`sonuçlanır:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Rota parametreleri

Yönlendirici, ilgili bileşen parametrelerini aynı ada (büyük/küçük harf duyarsız) doldurmak için rota parametrelerini kullanır:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

İsteğe bağlı parametreler desteklenmez. Önceki `@page` örnekte iki yönerge uygulanır. İlk bir parametre olmadan bileşene navigasyon izin verir. İkinci `@page` yönerge `{text}` rota parametresini alır ve `Text` değeri özelliğe atar.

## <a name="route-constraints"></a>Rota kısıtlamaları

Bir rota kısıtlaması, bir bileşene bir rota kesiminde tür eşleştirmesi zorlar.

Aşağıdaki örnekte, `Users` bileşene giden yol yalnızca aşağıdaki durumlarda eşleşir:

* İstek `Id` URL'sinde bir rota kesimi bulunur.
* Segment `Id` bir karşıcıdır`int`( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Aşağıdaki tabloda gösterilen rota kısıtlamaları kullanılabilir. Değişmez kültürle eşleşen rota kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.

| Kısıtlama | Örnek           | Örnek Eşleşmeler                                                                  | Sabit<br>kültür<br>eşleştirme |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet                              |

> [!WARNING]
> URL'yi doğrulayan ve CLR türüne dönüştürülen `int` (örneğin `DateTime`veya) her zaman değişmez kültürü kullanan yol kısıtlamaları. Bu kısıtlamalar, URL'nin yerelleştirilebilir olmadığını varsayar.

### <a name="routing-with-urls-that-contain-dots"></a>Nokta içeren URL'lerle yönlendirme

Blazor Server uygulamalarında, *_Host.cshtml'deki* varsayılan rota `/` ( ).`@page "/"` Nokta (`.`) içeren bir istek URL'si, URL bir dosya istemek için göründüğünden varsayılan rotayla eşleşmiyor. Bir Blazor uygulaması, var olmayan statik bir dosya için *404 - Bulunamadı* yanıtı döndürür. Nokta içeren yolları kullanmak için *_Host.cshtml'i* aşağıdaki rota şablonuyla yapılandırın:

```cshtml
@page "/{**path}"
```

Şablon `"/{**path}"` şunları içerir:

* Çift yıldız işareti *catch-all* sözdizimi (`**`) ileri kesikleri kodlamadan birden`/`çok klasör sınırları boyunca yolu yakalamak için ( ).
* `path`rota parametre adı.

> [!NOTE]
> *Catch-all* parametre sözdizimi (`*`/`**`) Razor bileşenlerinde **desteklenmez** (*.razor*).

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>NavLink bileşeni

Navigasyon `NavLink` bağlantıları oluştururken HTML köprü`<a>`öğeleri () yerine bir bileşen kullanın. Bileşen, `NavLink` geçerli URL `<a>` ile `href` eşleşip eşleşmediğine `active` bağlı olarak bir CSS sınıfını geçişlemesi dışında bir öğe gibi olur. Sınıf, `active` kullanıcının görüntülenen gezinme bağlantıları arasında hangi sayfanın etkin sayfa olduğunu anlamanıza yardımcı olur.

Aşağıdaki `NavMenu` bileşen, bileşenlerin nasıl kullanılacağını `NavLink` gösteren bir [Bootstrap](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Öğenin `NavLinkMatch` `Match` özniteliğine atayabileceğiniz iki seçenek vardır: `<NavLink>`

* `NavLinkMatch.All`&ndash; Geçerli `NavLink` URL'nin tamamıyla eşleştiğinde etkindir.
* `NavLinkMatch.Prefix`(*varsayılan*) &ndash; Geçerli `NavLink` URL'nin herhangi bir önekiyle eşleştiğinde etkindir.

Önceki örnekte, Ev `NavLink` `href=""` ev URL'si eşleşir ve yalnızca uygulamanın `active` varsayılan temel yol URL'sinde (örneğin) `https://localhost:5001/`CSS sınıfını alır. İkinci `NavLink` kullanıcı `active` önek ile herhangi bir `MyComponent` URL ziyaret ettiğinde `https://localhost:5001/MyComponent` sınıf `https://localhost:5001/MyComponent/AnotherSegment`alır (örneğin, ve).

Ek `NavLink` bileşen öznitelikleri işlenen bağlantı etiketine aktarılır. Aşağıdaki örnekte, `NavLink` bileşen özniteliği içerir: `target`

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI ve navigasyon durumu yardımcıları

URI'lerle çalışmak ve C# kodunda gezinmek için kullanın. <xref:Microsoft.AspNetCore.Components.NavigationManager> `NavigationManager`aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ------ | ----------- |
| Urı | Geçerli mutlak URI alır. |
| Baseuri | Mutlak bir URI üretmek için göreceli URI yollarına hazırlanabilen temel URI'yi (sondaki bir eğik çizgiyle) alır. `BaseUri` Genellikle, `href` *belgenin* Blazor `<base>` wwwroot/index.html (WebAssembly) veya *Pages/_Host.cshtml* (Server)Blazor öğesindeki özniteliğe karşılık gelir. |
| Gezinme | Belirtilen URI'ye doğru iletin. `true`Ise: `forceLoad`<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Tarayıcı, URI normalde istemci tarafı yönlendirici tarafından işlenir olsun veya olmasın, sunucudan yeni sayfa yüklemek zorunda kalır.</li></ul> |
| Konum Değiştirildi | Gezinti yeri değiştiğinde ateşleyen bir olay. |
| Toabsoluteuri | Göreceli bir URI'yi mutlak bir URI'ye dönüştürür. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Bir temel URI (örneğin, bir URI `GetBaseUri`daha önce döndürülen) göz önüne alındığında, temel URI öneki göre bir URI göreli mutlak bir URI dönüştürür. |

Düğme seçildiğinde aşağıdaki bileşen uygulamanın `Counter` bileşenine yönlendirilir:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Aşağıdaki bileşen, değiştirilen bir konumu işler. Yöntem, `HandleLocationChanged` çerçeve tarafından `Dispose` çağrıldığında çözülz. Yöntemin çözülmesi bileşenin çöp toplaması için izin verir.

```razor
@implement IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olay hakkında aşağıdaki bilgileri sağlar:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Yeni konumun URL'si.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Eğer, Blazor tarayıcıdan navigasyon ele geçirdi. If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) gezintisinin oluşmasına neden oldu.

Bileşen imhası hakkında daha <xref:blazor/lifecycle#component-disposal-with-idisposable>fazla bilgi için bkz.
