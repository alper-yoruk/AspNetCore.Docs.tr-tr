---
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor Yönlendirme

[Luke Latham](https://github.com/guardrex) tarafından

İsteklerin nasıl yönlendirileceğini ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .

## <a name="aspnet-core-endpoint-routing-integration"></a>Uç nokta yönlendirme tümleştirmesi ASP.NET Core

BlazorSunucu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir. ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

En yaygın yapılandırma, tüm isteklerin Razor sunucu uygulamasının sunucu tarafı bölümü için konak görevi gören bir sayfaya yönlendirilmesidir Blazor . Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır. Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır. Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir. Bu, uygulamanın sunucu uygulamasını kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor .

## <a name="route-templates"></a>Rota şablonları

<xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar. <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen *app. Razor* dosyasında görünür:

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

Bir yönergeyle bir *. Razor* dosyası `@page` derlendiğinde, oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir olarak sağlanır.

Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:

* ' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> istediğiniz parametrelerle birlikte alır.
* Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.

İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz. Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` . *Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir. Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..

Birden çok yol şablonu, bir bileşene uygulanabilir. Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL 'Lerin doğru şekilde çözülmesi için, uygulamanın `<base>` *Wwwroot/index.html* dosyası ( Blazor webassembly) veya *Pages/_Host. cshtml* dosyasında ( Blazor sunucu), özniteliğinde belirtilen uygulama temel yolu ile bir etiket `href` ( `<base href="/">` ) içermesi gerekir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağla

<xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.

*App. Razor* dosyasında, <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bileşenin Şablon parametresinde özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> :

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

`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir. İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts> ..

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

<xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın. Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` . Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir. Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteği ile sonuçlanır `Component1` :

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

İsteğe bağlı parametreler desteklenmez. `@page`Önceki örnekte iki yönergeler uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.

## <a name="route-constraints"></a>Yol kısıtlamaları

Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.

Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:

* `Id`Istek URL 'sinde bir yol kesimi var.
* `Id`Segment bir tamsayıdır ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur. Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.

| Kısıtlaması | Örnek           | Örnek eşleşmeler                                                                  | Bilmesi<br>kültür<br>eşleştirme |
| ---
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

----- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

--------- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---------------------------------------- | :---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet |

> [!WARNING]
> URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.

### <a name="routing-with-urls-that-contain-dots"></a>Noktalar içeren URL 'lerle yönlendirme

BlazorSunucu uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` ( `@page "/"` ). `.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor. Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür. Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:

```cshtml
@page "/{**path}"
```

`"/{**path}"`Şablon şunları içerir:

* Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .
* `path`rota parametresi adı.

> [!NOTE]
> *Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde (*. Razor*) desteklenmez.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Gezinti bağlantısı bileşeni

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` . Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır. `active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.

Aşağıdaki `NavMenu` Bileşen, bileşenlerin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>&ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Tüm geçerli URL ile eşleştiğinde etkin olur.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*varsayılan*) &ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink>GEÇERLI URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.

Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ). İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).

Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir. Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI ve gezinti durumu yardımcıları

<xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın. <xref:Microsoft.AspNetCore.Components.NavigationManager>Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ---
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

--- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Geçerli mutlak URI 'yi alır. | | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır. Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` *Wwwroot/index.html* ( Blazor webassembly) veya *Pages/_Host. cshtml* (sunucu) içindeki belge öğesindeki özniteliğe karşılık gelir Blazor . | | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Belirtilen URI 'ye gider. `forceLoad`Şu ise `true` :<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</li></ul> | | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Gezinti konumu değiştiğinde harekete gelen bir olay. | | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür. | | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür. |

Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:

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

Aşağıdaki bileşen bir konum değiştirme olayını ayarıyla işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . `HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir. Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.

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

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Yeni konumun URL 'si.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir. Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.

Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/lifecycle#component-disposal-with-idisposable> ..
