---
title: ASP.NET Core Blazor yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 017fd4d3ab45b75355dabb400ff0e5cbf7009d82
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771226"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor yönlendirme

[Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

İsteklerin nasıl yönlendirileceğini ve Blazor uygulamalarında gezinti bağlantıları oluşturmak için `NavLink` bileşenin nasıl kullanılacağını öğrenin.

## <a name="aspnet-core-endpoint-routing-integration"></a>Uç nokta yönlendirme tümleştirmesi ASP.NET Core

Blazor sunucusu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir. ASP.NET Core bir uygulama, `MapBlazorHub` içindeki `Startup.Configure`etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde yapılandırılmıştır:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

En yaygın yapılandırma, tüm istekleri Razor sayfasına yönlendirmesidir ve bu, Blazor Server uygulamasının sunucu tarafı bölümü için ana bilgisayar görevi görür. Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır. Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır. Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir. Bu, uygulamanın Blazor sunucu uygulamasıyla kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir.

## <a name="route-templates"></a>Rota şablonları

Bileşeni `Router` , belirtilen bir rota ile her bileşene yönlendirmeyi sağlar. `Router` Bileşen *app. Razor* dosyasında görünür:

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

Bir `@page` yönergeyle bir <xref:Microsoft.AspNetCore.Components.RouteAttribute> *. Razor* dosyası derlendiğinde, oluşturulan sınıf, yol şablonunu belirten bir olarak sağlanır.

Çalışma zamanında, `RouteView` bileşen:

* `RouteData` ' İ istediğiniz parametrelerle `Router` birlikte alır.
* Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.

İsteğe bağlı olarak, bir `DefaultLayout` düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz. Varsayılan Blazor şablonları `MainLayout` bileşeni belirler. *Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir. Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts>..

Birden çok yol şablonu, bir bileşene uygulanabilir. Aşağıdaki bileşen ve `/BlazorRoute` `/DifferentBlazorRoute`için isteklere yanıt verir:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL 'Lerin doğru bir şekilde çözülmesi için, uygulamanın *Wwwroot/index.html* dosyasına `<base>` (Blazor Webassembly) veya *Pages/_Host. cshtml* dosyasında (Blazor Server) ( `href` `<base href="/">`) özniteliğinde belirtilen uygulama temel yolu ile bir etiket içermesi gerekir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağla

`Router` Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.

*App. Razor* dosyasında, `NotFound` `Router` bileşenin Şablon parametresinde özel içerik ayarlayın:

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

`<NotFound>` Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir. `NotFound` İçeriğe varsayılan bir düzen uygulamak için bkz <xref:blazor/layouts>..

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

Bileşen için `AdditionalAssemblies` yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın. `Router` Belirtilen derlemeler, `AppAssembly`belirtilen derlemeye ek olarak değerlendirilir. Aşağıdaki örnekte, `Component1` başvurulan bir sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir. Aşağıdaki `AdditionalAssemblies` örnek, için `Component1`yönlendirme desteği ile sonuçlanır:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Rota parametreleri

Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:

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

İsteğe bağlı parametreler desteklenmez. Önceki `@page` örnekte iki yönergeler uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.

## <a name="route-constraints"></a>Yol kısıtlamaları

Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.

Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:

* İstek `Id` URL 'sinde bir yol kesimi var.
* `Id` Segment bir tamsayıdır (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur. Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.

| Kısıtlaması | Örnek           | Örnek eşleşmeler                                                                  | Bilmesi<br>kültür<br>eşleştirme |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |

> [!WARNING]
> URL 'YI doğrulayan ve bir CLR türüne ( `int` veya `DateTime`gibi) dönüştürülen yol kısıtlamaları, her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.

### <a name="routing-with-urls-that-contain-dots"></a>Noktalar içeren URL 'lerle yönlendirme

Blazor Server uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` (`@page "/"`). URL bir dosya isteyecek şekilde göründüğünden, nokta`.`() içeren BIR istek URL 'si varsayılan yol tarafından eşleşmiyor. Bir Blazor uygulaması mevcut olmayan bir statik dosya için *404-Found* yanıtı döndürür. Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:

```cshtml
@page "/{**path}"
```

`"/{**path}"` Şablon şunları içerir:

* Ters eğik çizgi (`/`) kodlaması olmadan birden`**`çok klasör sınırlarındaki yolu yakalamak için çift yıldız *catch-all* söz dizimi ().
* `path`rota parametresi adı.

> [!NOTE]
> *Catch-all* parametre sözdizimi (`*`/`**`) Razor bileşenlerinde (*. Razor* **) desteklenmez.**

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Gezinti bağlantısı bileşeni

Gezinti bağlantıları `NavLink` oluştururken, HTML köprü öğelerinin (`<a>`) yerine bir bileşen kullanın. Bir `NavLink` bileşen, geçerli URL `<a>` `href` ile eşleşip eşleşmediğini temel alarak bir `active` CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır. `active` Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.

Aşağıdaki `NavMenu` bileşen, bileşenlerin nasıl [Bootstrap](https://getbootstrap.com/docs/) kullanılacağını `NavLink` gösteren bir önyükleme gezinti çubuğu oluşturur:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki `NavLinkMatch` `Match` seçenek `<NavLink>` vardır:

* `NavLinkMatch.All`&ndash; Tüm `NavLink` geçerli URL ile eşleştiğinde etkin olur.
* `NavLinkMatch.Prefix`(*varsayılan*) &ndash; Geçerli `NavLink` URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.

Yukarıdaki `NavLink` `href=""` örnekte, ana giriş URL 'siyle eşleşir ve yalnızca uygulamanın varsayılan temel yol URL `active` 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/`). İkincisi `NavLink` , Kullanıcı ön `active` eki olan herhangi bir `MyComponent` URL 'yi ziyaret ettiğinde sınıfı alır (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment`).

Ek `NavLink` bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir. Aşağıdaki örnekte, `NavLink` bileşen `target` özniteliğini içerir:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI ve gezinti durumu yardımcıları

C# <xref:Microsoft.AspNetCore.Components.NavigationManager> kodunda URI ve gezinme ile çalışmak için kullanın. `NavigationManager`Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ------ | ----------- |
| Kullanılmamışsa | Geçerli mutlak URI 'yi alır. |
| BaseUri | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır. Genellikle, `BaseUri` *Wwwroot/index.html* (Blazor webassembly) veya *Pages/_Host. cshtml* (Blazor sunucu) içindeki belge `href` `<base>` öğesindeki özniteliğe karşılık gelir. |
| Gezin | Belirtilen URI 'ye gider. `forceLoad` Şu `true`ise:<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</li></ul> |
| LocationChanged | Gezinti konumu değiştiğinde harekete gelen bir olay. |
| ToAbsoluteUri | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Temel URI (örneğin, daha önce tarafından `GetBaseUri`döndürülen bir URI) verildiğinde, mutlak bir URI 'YI taban URI önekine göre bir URI 'ye dönüştürür. |

Aşağıdaki bileşen, düğme seçildiğinde uygulamanın `Counter` bileşenine gider:

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

Aşağıdaki bileşen bir konum değişti olayını işler. `HandleLocationChanged` Yöntemi, Framework tarafından çağrıldığında yok edilir `Dispose` . Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.

```razor
@implements IDisposable
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

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olayla ilgili aşağıdaki bilgileri sağlar:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Yeni konumun URL 'si.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Varsa `true`, Blazor tarayıcıdan gezinme ele geçirilebilir. İse `false`, [navigationmanager. Navigate,](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) gezintinin oluşmasına neden oldu.

Bileşen elden çıkarma hakkında daha fazla bilgi için <xref:blazor/lifecycle#component-disposal-with-idisposable>bkz..
