---
<span data-ttu-id="1e22a-101">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-102">'Blazor'</span></span>
- <span data-ttu-id="1e22a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-103">'Identity'</span></span>
- <span data-ttu-id="1e22a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-105">'Razor'</span></span>
- <span data-ttu-id="1e22a-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="1e22a-107">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e22a-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="1e22a-108">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="1e22a-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1e22a-109">İsteklerin nasıl yönlendirileceğini ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .</span><span class="sxs-lookup"><span data-stu-id="1e22a-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="1e22a-110">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e22a-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="1e22a-111">Sunucu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="1e22a-112">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1e22a-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="1e22a-113">En yaygın yapılandırma, tüm isteklerin Razor sunucu uygulamasının sunucu tarafı bölümü için konak görevi gören bir sayfaya yönlendirilmesidir Blazor .</span><span class="sxs-lookup"><span data-stu-id="1e22a-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="1e22a-114">Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="1e22a-115">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="1e22a-116">Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="1e22a-117">Bu, uygulamanın sunucu uygulamasını kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor .</span><span class="sxs-lookup"><span data-stu-id="1e22a-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="1e22a-118">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="1e22a-118">Route templates</span></span>

<span data-ttu-id="1e22a-119"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="1e22a-120"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen *app. Razor* dosyasında görünür:</span><span class="sxs-lookup"><span data-stu-id="1e22a-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="1e22a-121">Bir yönergeyle bir *. Razor* dosyası `@page` derlendiğinde, oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="1e22a-122">Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:</span><span class="sxs-lookup"><span data-stu-id="1e22a-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="1e22a-123">' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> istediğiniz parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="1e22a-124">Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.</span><span class="sxs-lookup"><span data-stu-id="1e22a-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="1e22a-125">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e22a-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="1e22a-126">Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` .</span><span class="sxs-lookup"><span data-stu-id="1e22a-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="1e22a-127">*Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="1e22a-128">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="1e22a-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="1e22a-129">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1e22a-130">Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="1e22a-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="1e22a-131">URL 'Lerin doğru şekilde çözülmesi için, uygulamanın `<base>` *Wwwroot/index.html* dosyası ( Blazor webassembly) veya *Pages/_Host. cshtml* dosyasında ( Blazor sunucu), özniteliğinde belirtilen uygulama temel yolu ile bir etiket `href` ( `<base href="/">` ) içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="1e22a-132">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="1e22a-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="1e22a-133">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="1e22a-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="1e22a-134"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="1e22a-135">*App. Razor* dosyasında, <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bileşenin Şablon parametresinde özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> :</span><span class="sxs-lookup"><span data-stu-id="1e22a-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="1e22a-136">`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="1e22a-137">İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="1e22a-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="1e22a-138">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="1e22a-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="1e22a-139"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e22a-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="1e22a-140">Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="1e22a-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="1e22a-141">Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="1e22a-142">Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteği ile sonuçlanır `Component1` :</span><span class="sxs-lookup"><span data-stu-id="1e22a-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="1e22a-143">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="1e22a-143">Route parameters</span></span>

<span data-ttu-id="1e22a-144">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="1e22a-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="1e22a-145">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1e22a-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="1e22a-146">`@page`Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="1e22a-147">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1e22a-148">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="1e22a-149">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="1e22a-149">Route constraints</span></span>

<span data-ttu-id="1e22a-150">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="1e22a-151">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="1e22a-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="1e22a-152">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="1e22a-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="1e22a-153">`Id`Segment bir tamsayıdır ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="1e22a-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="1e22a-154">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="1e22a-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="1e22a-155">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="1e22a-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="1e22a-156">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="1e22a-156">Constraint</span></span> | <span data-ttu-id="1e22a-157">Örnek</span><span class="sxs-lookup"><span data-stu-id="1e22a-157">Example</span></span>           | <span data-ttu-id="1e22a-158">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="1e22a-158">Example Matches</span></span>                                                                  | <span data-ttu-id="1e22a-159">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="1e22a-159">Invariant</span></span><br><span data-ttu-id="1e22a-160">kültür</span><span class="sxs-lookup"><span data-stu-id="1e22a-160">culture</span></span><br><span data-ttu-id="1e22a-161">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="1e22a-161">matching</span></span> |
| ---
<span data-ttu-id="1e22a-162">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-163">'Blazor'</span></span>
- <span data-ttu-id="1e22a-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-164">'Identity'</span></span>
- <span data-ttu-id="1e22a-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-166">'Razor'</span></span>
- <span data-ttu-id="1e22a-167">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-168">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-169">'Blazor'</span></span>
- <span data-ttu-id="1e22a-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-170">'Identity'</span></span>
- <span data-ttu-id="1e22a-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-172">'Razor'</span></span>
- <span data-ttu-id="1e22a-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-174">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-175">'Blazor'</span></span>
- <span data-ttu-id="1e22a-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-176">'Identity'</span></span>
- <span data-ttu-id="1e22a-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-178">'Razor'</span></span>
- <span data-ttu-id="1e22a-179">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-179">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-180">----- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-181">'Blazor'</span></span>
- <span data-ttu-id="1e22a-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-182">'Identity'</span></span>
- <span data-ttu-id="1e22a-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-184">'Razor'</span></span>
- <span data-ttu-id="1e22a-185">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-186">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-187">'Blazor'</span></span>
- <span data-ttu-id="1e22a-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-188">'Identity'</span></span>
- <span data-ttu-id="1e22a-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-190">'Razor'</span></span>
- <span data-ttu-id="1e22a-191">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-192">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-193">'Blazor'</span></span>
- <span data-ttu-id="1e22a-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-194">'Identity'</span></span>
- <span data-ttu-id="1e22a-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-196">'Razor'</span></span>
- <span data-ttu-id="1e22a-197">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-198">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-199">'Blazor'</span></span>
- <span data-ttu-id="1e22a-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-200">'Identity'</span></span>
- <span data-ttu-id="1e22a-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-202">'Razor'</span></span>
- <span data-ttu-id="1e22a-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-204">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-205">'Blazor'</span></span>
- <span data-ttu-id="1e22a-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-206">'Identity'</span></span>
- <span data-ttu-id="1e22a-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-208">'Razor'</span></span>
- <span data-ttu-id="1e22a-209">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-210">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-211">'Blazor'</span></span>
- <span data-ttu-id="1e22a-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-212">'Identity'</span></span>
- <span data-ttu-id="1e22a-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-214">'Razor'</span></span>
- <span data-ttu-id="1e22a-215">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-215">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-216">--------- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-217">'Blazor'</span></span>
- <span data-ttu-id="1e22a-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-218">'Identity'</span></span>
- <span data-ttu-id="1e22a-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-220">'Razor'</span></span>
- <span data-ttu-id="1e22a-221">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-222">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-223">'Blazor'</span></span>
- <span data-ttu-id="1e22a-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-224">'Identity'</span></span>
- <span data-ttu-id="1e22a-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-226">'Razor'</span></span>
- <span data-ttu-id="1e22a-227">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-228">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-229">'Blazor'</span></span>
- <span data-ttu-id="1e22a-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-230">'Identity'</span></span>
- <span data-ttu-id="1e22a-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-232">'Razor'</span></span>
- <span data-ttu-id="1e22a-233">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-234">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-235">'Blazor'</span></span>
- <span data-ttu-id="1e22a-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-236">'Identity'</span></span>
- <span data-ttu-id="1e22a-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-238">'Razor'</span></span>
- <span data-ttu-id="1e22a-239">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-240">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-241">'Blazor'</span></span>
- <span data-ttu-id="1e22a-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-242">'Identity'</span></span>
- <span data-ttu-id="1e22a-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-244">'Razor'</span></span>
- <span data-ttu-id="1e22a-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-246">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-247">'Blazor'</span></span>
- <span data-ttu-id="1e22a-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-248">'Identity'</span></span>
- <span data-ttu-id="1e22a-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-250">'Razor'</span></span>
- <span data-ttu-id="1e22a-251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-252">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-253">'Blazor'</span></span>
- <span data-ttu-id="1e22a-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-254">'Identity'</span></span>
- <span data-ttu-id="1e22a-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-256">'Razor'</span></span>
- <span data-ttu-id="1e22a-257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-258">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-259">'Blazor'</span></span>
- <span data-ttu-id="1e22a-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-260">'Identity'</span></span>
- <span data-ttu-id="1e22a-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-262">'Razor'</span></span>
- <span data-ttu-id="1e22a-263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-264">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-265">'Blazor'</span></span>
- <span data-ttu-id="1e22a-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-266">'Identity'</span></span>
- <span data-ttu-id="1e22a-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-268">'Razor'</span></span>
- <span data-ttu-id="1e22a-269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-270">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-271">'Blazor'</span></span>
- <span data-ttu-id="1e22a-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-272">'Identity'</span></span>
- <span data-ttu-id="1e22a-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-274">'Razor'</span></span>
- <span data-ttu-id="1e22a-275">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-276">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-277">'Blazor'</span></span>
- <span data-ttu-id="1e22a-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-278">'Identity'</span></span>
- <span data-ttu-id="1e22a-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-280">'Razor'</span></span>
- <span data-ttu-id="1e22a-281">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-282">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-283">'Blazor'</span></span>
- <span data-ttu-id="1e22a-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-284">'Identity'</span></span>
- <span data-ttu-id="1e22a-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-286">'Razor'</span></span>
- <span data-ttu-id="1e22a-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-288">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-289">'Blazor'</span></span>
- <span data-ttu-id="1e22a-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-290">'Identity'</span></span>
- <span data-ttu-id="1e22a-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-292">'Razor'</span></span>
- <span data-ttu-id="1e22a-293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-294">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-295">'Blazor'</span></span>
- <span data-ttu-id="1e22a-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-296">'Identity'</span></span>
- <span data-ttu-id="1e22a-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-298">'Razor'</span></span>
- <span data-ttu-id="1e22a-299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-300">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-301">'Blazor'</span></span>
- <span data-ttu-id="1e22a-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-302">'Identity'</span></span>
- <span data-ttu-id="1e22a-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-304">'Razor'</span></span>
- <span data-ttu-id="1e22a-305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-306">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-307">'Blazor'</span></span>
- <span data-ttu-id="1e22a-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-308">'Identity'</span></span>
- <span data-ttu-id="1e22a-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-310">'Razor'</span></span>
- <span data-ttu-id="1e22a-311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-312">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-313">'Blazor'</span></span>
- <span data-ttu-id="1e22a-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-314">'Identity'</span></span>
- <span data-ttu-id="1e22a-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-316">'Razor'</span></span>
- <span data-ttu-id="1e22a-317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-318">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-319">'Blazor'</span></span>
- <span data-ttu-id="1e22a-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-320">'Identity'</span></span>
- <span data-ttu-id="1e22a-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-322">'Razor'</span></span>
- <span data-ttu-id="1e22a-323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-324">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-325">'Blazor'</span></span>
- <span data-ttu-id="1e22a-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-326">'Identity'</span></span>
- <span data-ttu-id="1e22a-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-328">'Razor'</span></span>
- <span data-ttu-id="1e22a-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-330">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-331">'Blazor'</span></span>
- <span data-ttu-id="1e22a-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-332">'Identity'</span></span>
- <span data-ttu-id="1e22a-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-334">'Razor'</span></span>
- <span data-ttu-id="1e22a-335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-336">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-337">'Blazor'</span></span>
- <span data-ttu-id="1e22a-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-338">'Identity'</span></span>
- <span data-ttu-id="1e22a-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-340">'Razor'</span></span>
- <span data-ttu-id="1e22a-341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-342">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-343">'Blazor'</span></span>
- <span data-ttu-id="1e22a-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-344">'Identity'</span></span>
- <span data-ttu-id="1e22a-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-346">'Razor'</span></span>
- <span data-ttu-id="1e22a-347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-348">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-349">'Blazor'</span></span>
- <span data-ttu-id="1e22a-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-350">'Identity'</span></span>
- <span data-ttu-id="1e22a-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-352">'Razor'</span></span>
- <span data-ttu-id="1e22a-353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-354">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-355">'Blazor'</span></span>
- <span data-ttu-id="1e22a-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-356">'Identity'</span></span>
- <span data-ttu-id="1e22a-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-358">'Razor'</span></span>
- <span data-ttu-id="1e22a-359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-360">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-361">'Blazor'</span></span>
- <span data-ttu-id="1e22a-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-362">'Identity'</span></span>
- <span data-ttu-id="1e22a-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-364">'Razor'</span></span>
- <span data-ttu-id="1e22a-365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-366">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-367">'Blazor'</span></span>
- <span data-ttu-id="1e22a-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-368">'Identity'</span></span>
- <span data-ttu-id="1e22a-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-370">'Razor'</span></span>
- <span data-ttu-id="1e22a-371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-372">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-373">'Blazor'</span></span>
- <span data-ttu-id="1e22a-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-374">'Identity'</span></span>
- <span data-ttu-id="1e22a-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-376">'Razor'</span></span>
- <span data-ttu-id="1e22a-377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-378">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-379">'Blazor'</span></span>
- <span data-ttu-id="1e22a-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-380">'Identity'</span></span>
- <span data-ttu-id="1e22a-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-382">'Razor'</span></span>
- <span data-ttu-id="1e22a-383">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-384">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-385">'Blazor'</span></span>
- <span data-ttu-id="1e22a-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-386">'Identity'</span></span>
- <span data-ttu-id="1e22a-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-388">'Razor'</span></span>
- <span data-ttu-id="1e22a-389">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-390">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-391">'Blazor'</span></span>
- <span data-ttu-id="1e22a-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-392">'Identity'</span></span>
- <span data-ttu-id="1e22a-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-394">'Razor'</span></span>
- <span data-ttu-id="1e22a-395">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-396">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-397">'Blazor'</span></span>
- <span data-ttu-id="1e22a-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-398">'Identity'</span></span>
- <span data-ttu-id="1e22a-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-400">'Razor'</span></span>
- <span data-ttu-id="1e22a-401">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-402">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-403">'Blazor'</span></span>
- <span data-ttu-id="1e22a-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-404">'Identity'</span></span>
- <span data-ttu-id="1e22a-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-406">'Razor'</span></span>
- <span data-ttu-id="1e22a-407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-408">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-409">'Blazor'</span></span>
- <span data-ttu-id="1e22a-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-410">'Identity'</span></span>
- <span data-ttu-id="1e22a-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-412">'Razor'</span></span>
- <span data-ttu-id="1e22a-413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-414">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-415">'Blazor'</span></span>
- <span data-ttu-id="1e22a-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-416">'Identity'</span></span>
- <span data-ttu-id="1e22a-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-418">'Razor'</span></span>
- <span data-ttu-id="1e22a-419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-420">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-421">'Blazor'</span></span>
- <span data-ttu-id="1e22a-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-422">'Identity'</span></span>
- <span data-ttu-id="1e22a-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-424">'Razor'</span></span>
- <span data-ttu-id="1e22a-425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-426">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-427">'Blazor'</span></span>
- <span data-ttu-id="1e22a-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-428">'Identity'</span></span>
- <span data-ttu-id="1e22a-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-430">'Razor'</span></span>
- <span data-ttu-id="1e22a-431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-432">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-433">'Blazor'</span></span>
- <span data-ttu-id="1e22a-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-434">'Identity'</span></span>
- <span data-ttu-id="1e22a-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-436">'Razor'</span></span>
- <span data-ttu-id="1e22a-437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-438">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-439">'Blazor'</span></span>
- <span data-ttu-id="1e22a-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-440">'Identity'</span></span>
- <span data-ttu-id="1e22a-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-442">'Razor'</span></span>
- <span data-ttu-id="1e22a-443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-443">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-444">---------------------------------------- | :---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-445">'Blazor'</span></span>
- <span data-ttu-id="1e22a-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-446">'Identity'</span></span>
- <span data-ttu-id="1e22a-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-448">'Razor'</span></span>
- <span data-ttu-id="1e22a-449">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-450">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-451">'Blazor'</span></span>
- <span data-ttu-id="1e22a-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-452">'Identity'</span></span>
- <span data-ttu-id="1e22a-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-454">'Razor'</span></span>
- <span data-ttu-id="1e22a-455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-456">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-457">'Blazor'</span></span>
- <span data-ttu-id="1e22a-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-458">'Identity'</span></span>
- <span data-ttu-id="1e22a-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-460">'Razor'</span></span>
- <span data-ttu-id="1e22a-461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-462">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-463">'Blazor'</span></span>
- <span data-ttu-id="1e22a-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-464">'Identity'</span></span>
- <span data-ttu-id="1e22a-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-466">'Razor'</span></span>
- <span data-ttu-id="1e22a-467">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-468">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-469">'Blazor'</span></span>
- <span data-ttu-id="1e22a-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-470">'Identity'</span></span>
- <span data-ttu-id="1e22a-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-472">'Razor'</span></span>
- <span data-ttu-id="1e22a-473">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-474">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-475">'Blazor'</span></span>
- <span data-ttu-id="1e22a-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-476">'Identity'</span></span>
- <span data-ttu-id="1e22a-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-478">'Razor'</span></span>
- <span data-ttu-id="1e22a-479">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-480">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-481">'Blazor'</span></span>
- <span data-ttu-id="1e22a-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-482">'Identity'</span></span>
- <span data-ttu-id="1e22a-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-484">'Razor'</span></span>
- <span data-ttu-id="1e22a-485">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-486">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-487">'Blazor'</span></span>
- <span data-ttu-id="1e22a-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-488">'Identity'</span></span>
- <span data-ttu-id="1e22a-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-490">'Razor'</span></span>
- <span data-ttu-id="1e22a-491">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-492">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-493">'Blazor'</span></span>
- <span data-ttu-id="1e22a-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-494">'Identity'</span></span>
- <span data-ttu-id="1e22a-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-496">'Razor'</span></span>
- <span data-ttu-id="1e22a-497">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-498">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-499">'Blazor'</span></span>
- <span data-ttu-id="1e22a-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-500">'Identity'</span></span>
- <span data-ttu-id="1e22a-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-502">'Razor'</span></span>
- <span data-ttu-id="1e22a-503">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-504">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-505">'Blazor'</span></span>
- <span data-ttu-id="1e22a-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-506">'Identity'</span></span>
- <span data-ttu-id="1e22a-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-508">'Razor'</span></span>
- <span data-ttu-id="1e22a-509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-510">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-511">'Blazor'</span></span>
- <span data-ttu-id="1e22a-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-512">'Identity'</span></span>
- <span data-ttu-id="1e22a-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-514">'Razor'</span></span>
- <span data-ttu-id="1e22a-515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-516">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-517">'Blazor'</span></span>
- <span data-ttu-id="1e22a-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-518">'Identity'</span></span>
- <span data-ttu-id="1e22a-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-520">'Razor'</span></span>
- <span data-ttu-id="1e22a-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-521">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet |</span><span class="sxs-lookup"><span data-stu-id="1e22a-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="1e22a-523">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="1e22a-524">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="1e22a-525">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e22a-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="1e22a-526">BlazorSunucu uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="1e22a-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="1e22a-527">`.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="1e22a-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="1e22a-528">Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="1e22a-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="1e22a-529">Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="1e22a-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="1e22a-530">`"/{**path}"`Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="1e22a-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="1e22a-531">Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .</span><span class="sxs-lookup"><span data-stu-id="1e22a-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="1e22a-532">`path`rota parametresi adı.</span><span class="sxs-lookup"><span data-stu-id="1e22a-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="1e22a-533">*Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1e22a-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="1e22a-534">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1e22a-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="1e22a-535">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="1e22a-535">NavLink component</span></span>

<span data-ttu-id="1e22a-536"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="1e22a-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="1e22a-537">Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="1e22a-538">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="1e22a-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="1e22a-539">Aşağıdaki `NavMenu` Bileşen, bileşenlerin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :</span><span class="sxs-lookup"><span data-stu-id="1e22a-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="1e22a-540"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="1e22a-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="1e22a-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> GEÇERLI URL 'nin tamamı eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="1e22a-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="1e22a-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*varsayılan*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="1e22a-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="1e22a-543">Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="1e22a-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="1e22a-544">İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="1e22a-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="1e22a-545">Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="1e22a-546">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="1e22a-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="1e22a-547">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="1e22a-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="1e22a-548">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="1e22a-548">URI and navigation state helpers</span></span>

<span data-ttu-id="1e22a-549"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e22a-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="1e22a-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e22a-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="1e22a-551">Üye</span><span class="sxs-lookup"><span data-stu-id="1e22a-551">Member</span></span> | <span data-ttu-id="1e22a-552">Açıklama</span><span class="sxs-lookup"><span data-stu-id="1e22a-552">Description</span></span> |
| ---
<span data-ttu-id="1e22a-553">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-554">'Blazor'</span></span>
- <span data-ttu-id="1e22a-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-555">'Identity'</span></span>
- <span data-ttu-id="1e22a-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-557">'Razor'</span></span>
- <span data-ttu-id="1e22a-558">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-558">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-559">--- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-560">'Blazor'</span></span>
- <span data-ttu-id="1e22a-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-561">'Identity'</span></span>
- <span data-ttu-id="1e22a-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-563">'Razor'</span></span>
- <span data-ttu-id="1e22a-564">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-565">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-566">'Blazor'</span></span>
- <span data-ttu-id="1e22a-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-567">'Identity'</span></span>
- <span data-ttu-id="1e22a-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-569">'Razor'</span></span>
- <span data-ttu-id="1e22a-570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1e22a-571">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e22a-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e22a-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-572">'Blazor'</span></span>
- <span data-ttu-id="1e22a-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e22a-573">'Identity'</span></span>
- <span data-ttu-id="1e22a-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e22a-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e22a-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e22a-575">'Razor'</span></span>
- <span data-ttu-id="1e22a-576">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1e22a-576">'SignalR' uid:</span></span> 

<span data-ttu-id="1e22a-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="1e22a-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="1e22a-579">Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` *Wwwroot/index.html* ( Blazor webassembly) veya *Pages/_Host. cshtml* (sunucu) içindeki belge öğesindeki özniteliğe karşılık gelir Blazor .</span><span class="sxs-lookup"><span data-stu-id="1e22a-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="1e22a-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="1e22a-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="1e22a-581">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="1e22a-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="1e22a-582">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="1e22a-583">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="1e22a-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="1e22a-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="1e22a-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="1e22a-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="1e22a-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="1e22a-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="1e22a-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="1e22a-587">Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="1e22a-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="1e22a-588">Aşağıdaki bileşen bir konum değiştirme olayını ayarıyla işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1e22a-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1e22a-589">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="1e22a-590">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="1e22a-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olayla ilgili aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="1e22a-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="1e22a-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Yeni konumun URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="1e22a-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="1e22a-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1e22a-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="1e22a-594">Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="1e22a-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="1e22a-595">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="1e22a-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
