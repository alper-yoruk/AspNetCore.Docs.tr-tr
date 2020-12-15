---
title: ASP.NET Core Blazor Yönlendirme
author: guardrex
description: Uygulamalarda istek yönlendirmeyi yönetmeyi ve Blazor Gezinti için uygulamalarda gezinti bağlantısı bileşenini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: ec183f4aadc6bafd8e77f9d97291ba3d47bd92f5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506935"
---
# <a name="aspnet-core-no-locblazor-routing"></a>ASP.NET Core Blazor Yönlendirme

[Luke Latham](https://github.com/guardrex) tarafından

Bu makalede, istek yönlendirmeyi yönetme ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağı hakkında bilgi edinin Blazor .

## <a name="route-templates"></a>Rota şablonları

<xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, Razor bir uygulamadaki bileşenlere yönlendirmeyi sağlar Blazor . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, `App` uygulamalar bileşeninde kullanılır Blazor .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

Razor `.razor` [ `@page` Yönergesi](xref:mvc/views/razor#page) içeren bir bileşen () derlendiğinde, oluşturulan bileşen sınıfı, <xref:Microsoft.AspNetCore.Components.RouteAttribute> bileşenin yol şablonunu belirten bir olarak sağlanır.

Uygulama başlatıldığında, yönlendirici olarak belirtilen derleme, `AppAssembly` uygulamanın bir öğesine sahip olan bileşenleri için rota bilgilerini toplayacak şekilde taranır <xref:Microsoft.AspNetCore.Components.RouteAttribute> .

Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:

* ' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> herhangi bir rota parametresiyle birlikte alır.
* Daha fazla iç içe yerleştirilmiş düzen dahil olmak üzere, belirtilen bileşeni [düzeniyle](xref:blazor/layouts)işler.

İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> , [ `@layout` yönergeyle](xref:blazor/layouts#specify-a-layout-in-a-component)bir düzen belirtmeyen bileşenler için Düzen sınıfı ile bir parametre belirtin. Çerçevenin Blazor Proje şablonları, `MainLayout` `Shared/MainLayout.razor` uygulamanın varsayılan düzeni olarak bileşenini () belirtir. Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..

Bileşenler birden çok [ `@page` yönergeleri](xref:mvc/views/razor#page)kullanarak birden çok yol şablonunu destekler. Aşağıdaki örnek bileşen ve için istekleri yükler `/BlazorRoute` `/DifferentBlazorRoute` .

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> URL 'Lerin doğru çözümlenmesi için, uygulamanın, `<base>` `wwwroot/index.html` Blazor WebAssembly `Pages/_Host.cshtml` Blazor Server özniteliğinde belirtilen uygulama temel yolu ile dosyasında () veya dosyasında () bir etiket içermesi gerekir `href` . Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağla

<xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.

`App`Bileşende bileşen şablonunda özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

Diğer etkileşimli bileşenler gibi etiketlerin içeriği olarak rastgele öğeler desteklenir `<NotFound>` . İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts#default-layout> ..

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

<xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın. Ek derlemeler, için belirtilen derlemeye ek olarak taranır `AppAssembly` . Aşağıdaki örnekte, `Component1` başvurulan [bileşen sınıfı kitaplığında](xref:blazor/components/class-libraries)tanımlanan yönlendirilebilir bir bileşendir. Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteğine neden olur `Component1` .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a>Rota parametreleri

Yönlendirici, karşılık gelen [bileşen parametrelerini](xref:blazor/components/index#component-parameters) aynı adla doldurmak için yol parametrelerini kullanır. Yol parametresi adları büyük/küçük harfe duyarlıdır. Aşağıdaki örnekte `text` parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar. İçin bir istek yapıldığında `/RouteParameter/amazing` , `<h1>` etiket içeriği olarak işlenir `Blazor is amazing!` .

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

İsteğe bağlı parametreler desteklenir. Aşağıdaki örnekte, `text` isteğe bağlı parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar. Segment yoksa, değeri `Text` olarak ayarlanır `fantastic` .

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

İsteğe bağlı parametreler desteklenmez. Aşağıdaki örnekte, iki [ `@page` yönergeler](xref:mvc/views/razor#page) uygulanır. İlk yönerge, bir parametre olmadan bileşene gezinmesine izin verir. İkinci yönerge, `{text}` yönlendirme parametresi değerini bileşenin `Text` özelliğine atar.

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

[`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) Farklı bir isteğe bağlı parametre değeriyle aynı bileşene uygulama gezintisine izin vermek için yerine kullanın. Önceki örneğe bağlı olarak, Kullanıcı ' dan veya ' den ' a kadar arasında `OnParametersSet` geziniyor olması gerekir `/RouteParameter` `/RouteParameter/amazing` `/RouteParameter/amazing` `/RouteParameter` :

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Yol kısıtlamaları

Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.

Aşağıdaki örnekte, `User` bileşen yolu yalnızca şu durumlarda eşleşir:

* `Id`Istek URL 'sinde bir yol kesimi var.
* `Id`Segment bir Integer ( `int` ) türüdür.

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

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
> URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.

## <a name="routing-with-urls-that-contain-dots"></a>Noktalar içeren URL 'lerle yönlendirme

Barındırılan Blazor WebAssembly ve Blazor Server uygulamalar için, sunucu tarafı varsayılan yol şablonu, BIR istek URL 'sinin son segmentinin bir dosyanın istendiği nokta () içerdiğini varsayar `.` . Örneğin, URL, `https://localhost.com:5001/example/some.thing` yönlendirici tarafından adlı bir dosya için bir istek olarak yorumlanır `some.thing` . Ek yapılandırma olmadan, bir uygulama bir  `some.thing` [ `@page` yönergeyle](xref:mvc/views/razor#page) bir bileşene yönlendirilecekse ve `some.thing` bir yol parametre değeri ise, uygulama 404-Found yanıtı döndürür. Bir nokta içeren bir veya daha fazla parametre içeren bir yol kullanmak için, uygulamanın rotayı özel bir şablonla yapılandırması gerekir.

`Example`URL 'nin son segmentinden bir yol parametresi alabilen aşağıdaki bileşeni göz önünde bulundurun.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

*`Server`* Barındırılan bir Blazor WebAssembly çözümün uygulamasının isteği yol parametresindeki bir noktayla yönlendirmesine izin vermek için `param` , içinde isteğe bağlı parametresine sahip bir geri dönüş dosya yolu şablonu ekleyin `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Bir uygulamayı, Blazor Server isteği Route parametresinde bir noktayla yönlendirmek üzere yapılandırmak için `param` , içinde isteğe bağlı parametresine sahip bir geri dönüş sayfası yol şablonu ekleyin `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Catch-all Route parametreleri

::: moniker range=">= aspnetcore-5.0"

Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri, bileşenlerinde desteklenir.

Catch-all Route parametreleri şunlardır:

* Yol segmenti adıyla eşleşecek şekilde adlandırılır. Adlandırma, büyük/küçük harfe duyarlı değildir.
* Bir `string` tür. Çerçeve otomatik atama sağlamaz.
* URL 'nin sonunda.

`Pages/CatchAll.razor`:

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

`/catch-all/this/is/a/test`Yol şablonuna sahıp URL için `/catch-all/{*pageRoute}` , değeri `PageRoute` olarak ayarlanır `this/is/a/test` .

Yakalanan yolun eğik çizgileri ve kesimleri kodu çözülür. Bir yol şablonu için `/catch-all/{*pageRoute}` , URL 'nin `/catch-all/this/is/a%2Ftest%2A` verir `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Catch-all Route parametreleri ASP.NET Core 5,0 veya üzeri sürümlerde desteklenir. Daha fazla bilgi için bu makalenin 5,0 sürümünü seçin.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>URI ve gezinti durumu yardımcıları

<xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI 'leri ve gezintiyi yönetmek için kullanın. <xref:Microsoft.AspNetCore.Components.NavigationManager> Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Geçerli mutlak URI 'yi alır. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır. Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` `wwwroot/index.html` ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` () içinde belge öğesindeki özniteliğine karşılık gelir Blazor Server . |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Belirtilen URI 'ye gider. `forceLoad`Şu ise `true` :<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Gezinti konumu değiştiğinde harekete gelen bir olay. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür. |

Olay için <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> , <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> gezinti olayları hakkında aşağıdaki bilgileri sağlar:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Yeni konumun URL 'SI.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir. Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.

Aşağıdaki bileşen:

* `Counter`Düğme seçildiğinde uygulamanın bileşenine () gider `Pages/Counter.razor` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .
* Abone olunarak konum değişti olayını işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .
  * `HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir. Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.
  * Düğme seçildiğinde günlükçü uygulama aşağıdaki bilgileri günlüğe kaydeder:

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> ..

## <a name="query-string-and-parse-parameters"></a>Sorgu dizesi ve ayrıştırma parametreleri

Bir isteğin sorgu dizesi özelliğinden elde edilir <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> :

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Bir sorgu dizesinin parametrelerini ayrıştırmak için:

* Bir uygulama, <xref:Microsoft.AspNetCore.WebUtilities> API 'yi kullanabilir. API uygulama için kullanılabilir değilse, [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)için uygulamanın proje dosyasına bir paket başvurusu ekleyin.
* İle sorgu dizesini ayrıştırdıktan sonra değeri alın <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

Aşağıdaki `ParseQueryString` bileşen örneği adlı bir sorgu dizesi parametre anahtarını ayrıştırır `ship` . Örneğin, URL sorgu dizesi anahtar-değer çifti `?ship=Tardis` içindeki değeri yakalar `Tardis` `queryValue` . Aşağıdaki örnekte, URL ile uygulamaya gidin `https://localhost:5001/parse-query-string?ship=Tardis` .

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a>`NavLink` bileşeni

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` . Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır. `active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur. İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> geçerli yol ile eşleştiğinde işlenen bağlantıya özel bır CSS sınıfı uygulamak için BIR CSS sınıfı adı atayın `href` .

Aşağıdaki `NavMenu` Bileşen, [`Bootstrap`](https://getbootstrap.com/docs/) bileşenlerin nasıl kullanılacağını gösteren bir gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> `NavMenu`Bileşen ( `NavMenu.razor` ), `Shared` proje şablonlarından oluşturulan bir uygulamanın klasöründe sağlanır Blazor .

<xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> GEÇERLI URL 'nin tamamı eşleştiğinde etkin olur.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*varsayılan*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.

Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ). İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `component` (örneğin, `https://localhost:5001/component` ve `https://localhost:5001/component/another-segment` ).

Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir. Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> BlazorAlt içeriği işleyen bir yol nedeniyle, `NavLink` bir döngü içindeki işleme bileşenleri, `for` `NavLink` (alt) bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa bir yerel dizin değişkeni gerektirir:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Bu senaryoda bir dizin değişkeni kullanılması yalnızca bileşeni değil, [alt içeriğinde](xref:blazor/components/index#child-content)bir döngü değişkeni kullanan **herhangi** bir alt bileşen için gereksinimdir `NavLink` .
>
> Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a>Uç nokta yönlendirme tümleştirmesi ASP.NET Core

*Bu bölüm yalnızca uygulamalar için geçerlidir Blazor Server .*

Blazor Server[ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir. ASP.NET Core bir uygulama, içindeki etkileşimli bileşenlere yönelik gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` .

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

Tipik yapılandırma tüm istekleri bir sayfaya yönlendirmesidir Razor ve bu, uygulamanın sunucu tarafı bölümü için ana bilgisayar görevi görür Blazor Server . Kural gereği, *ana bilgisayar* sayfası genellikle `_Host.cshtml` `Pages` uygulamanın klasöründe adlandırılır.

Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır. Geri dönüş yolu, diğer yollar eşleşmediğinden kullanılır. Bu, uygulamanın uygulamada bileşen yönlendirme ile kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor Server .

<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>Kök olmayan URL sunucusu barındırma için yapılandırma hakkında bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..
