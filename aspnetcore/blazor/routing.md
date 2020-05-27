---
<span data-ttu-id="f8009-101">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-102">'Blazor'</span></span>
- <span data-ttu-id="f8009-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-103">'Identity'</span></span>
- <span data-ttu-id="f8009-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-105">'Razor'</span></span>
- <span data-ttu-id="f8009-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="f8009-107">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="f8009-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="f8009-108">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="f8009-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8009-109">İsteklerin nasıl yönlendirileceğini ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8009-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="f8009-110">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8009-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="f8009-111">Sunucu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="f8009-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f8009-112">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8009-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="f8009-113">En yaygın yapılandırma, tüm isteklerin Razor sunucu uygulamasının sunucu tarafı bölümü için konak görevi gören bir sayfaya yönlendirilmesidir Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8009-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="f8009-114">Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f8009-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="f8009-115">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f8009-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="f8009-116">Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="f8009-117">Bu, uygulamanın sunucu uygulamasını kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8009-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="f8009-118">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="f8009-118">Route templates</span></span>

<span data-ttu-id="f8009-119"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f8009-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="f8009-120"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen *app. Razor* dosyasında görünür:</span><span class="sxs-lookup"><span data-stu-id="f8009-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="f8009-121">Bir yönergeyle bir *. Razor* dosyası `@page` derlendiğinde, oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f8009-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="f8009-122">Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:</span><span class="sxs-lookup"><span data-stu-id="f8009-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="f8009-123">' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> istediğiniz parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="f8009-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="f8009-124">Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.</span><span class="sxs-lookup"><span data-stu-id="f8009-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="f8009-125">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f8009-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="f8009-126">Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` .</span><span class="sxs-lookup"><span data-stu-id="f8009-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="f8009-127">*Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="f8009-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="f8009-128">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="f8009-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="f8009-129">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f8009-130">Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="f8009-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="f8009-131">URL 'Lerin doğru şekilde çözülmesi için, uygulamanın `<base>` *Wwwroot/index.html* dosyası ( Blazor webassembly) veya *Pages/_Host. cshtml* dosyasında ( Blazor sunucu), özniteliğinde belirtilen uygulama temel yolu ile bir etiket `href` ( `<base href="/">` ) içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f8009-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="f8009-132">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f8009-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="f8009-133">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="f8009-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="f8009-134"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f8009-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="f8009-135">*App. Razor* dosyasında, <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bileşenin Şablon parametresinde özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> :</span><span class="sxs-lookup"><span data-stu-id="f8009-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="f8009-136">`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="f8009-137">İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="f8009-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="f8009-138">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="f8009-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="f8009-139"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f8009-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="f8009-140">Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="f8009-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="f8009-141">Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="f8009-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="f8009-142">Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteği ile sonuçlanır `Component1` :</span><span class="sxs-lookup"><span data-stu-id="f8009-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="f8009-143">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="f8009-143">Route parameters</span></span>

<span data-ttu-id="f8009-144">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="f8009-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="f8009-145">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8009-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="f8009-146">`@page`Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f8009-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="f8009-147">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f8009-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f8009-148">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="f8009-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="f8009-149">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="f8009-149">Route constraints</span></span>

<span data-ttu-id="f8009-150">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="f8009-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="f8009-151">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="f8009-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="f8009-152">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="f8009-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="f8009-153">`Id`Segment bir tamsayıdır ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="f8009-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="f8009-154">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="f8009-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="f8009-155">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="f8009-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="f8009-156">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="f8009-156">Constraint</span></span> | <span data-ttu-id="f8009-157">Örnek</span><span class="sxs-lookup"><span data-stu-id="f8009-157">Example</span></span>           | <span data-ttu-id="f8009-158">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="f8009-158">Example Matches</span></span>                                                                  | <span data-ttu-id="f8009-159">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="f8009-159">Invariant</span></span><br><span data-ttu-id="f8009-160">kültür</span><span class="sxs-lookup"><span data-stu-id="f8009-160">culture</span></span><br><span data-ttu-id="f8009-161">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="f8009-161">matching</span></span> |
| ---
<span data-ttu-id="f8009-162">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-163">'Blazor'</span></span>
- <span data-ttu-id="f8009-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-164">'Identity'</span></span>
- <span data-ttu-id="f8009-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-166">'Razor'</span></span>
- <span data-ttu-id="f8009-167">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-168">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-169">'Blazor'</span></span>
- <span data-ttu-id="f8009-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-170">'Identity'</span></span>
- <span data-ttu-id="f8009-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-172">'Razor'</span></span>
- <span data-ttu-id="f8009-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-174">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-175">'Blazor'</span></span>
- <span data-ttu-id="f8009-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-176">'Identity'</span></span>
- <span data-ttu-id="f8009-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-178">'Razor'</span></span>
- <span data-ttu-id="f8009-179">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-179">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-180">----- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-181">'Blazor'</span></span>
- <span data-ttu-id="f8009-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-182">'Identity'</span></span>
- <span data-ttu-id="f8009-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-184">'Razor'</span></span>
- <span data-ttu-id="f8009-185">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-186">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-187">'Blazor'</span></span>
- <span data-ttu-id="f8009-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-188">'Identity'</span></span>
- <span data-ttu-id="f8009-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-190">'Razor'</span></span>
- <span data-ttu-id="f8009-191">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-192">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-193">'Blazor'</span></span>
- <span data-ttu-id="f8009-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-194">'Identity'</span></span>
- <span data-ttu-id="f8009-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-196">'Razor'</span></span>
- <span data-ttu-id="f8009-197">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-198">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-199">'Blazor'</span></span>
- <span data-ttu-id="f8009-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-200">'Identity'</span></span>
- <span data-ttu-id="f8009-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-202">'Razor'</span></span>
- <span data-ttu-id="f8009-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-204">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-205">'Blazor'</span></span>
- <span data-ttu-id="f8009-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-206">'Identity'</span></span>
- <span data-ttu-id="f8009-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-208">'Razor'</span></span>
- <span data-ttu-id="f8009-209">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-210">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-211">'Blazor'</span></span>
- <span data-ttu-id="f8009-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-212">'Identity'</span></span>
- <span data-ttu-id="f8009-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-214">'Razor'</span></span>
- <span data-ttu-id="f8009-215">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-215">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-216">--------- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-217">'Blazor'</span></span>
- <span data-ttu-id="f8009-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-218">'Identity'</span></span>
- <span data-ttu-id="f8009-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-220">'Razor'</span></span>
- <span data-ttu-id="f8009-221">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-222">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-223">'Blazor'</span></span>
- <span data-ttu-id="f8009-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-224">'Identity'</span></span>
- <span data-ttu-id="f8009-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-226">'Razor'</span></span>
- <span data-ttu-id="f8009-227">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-228">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-229">'Blazor'</span></span>
- <span data-ttu-id="f8009-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-230">'Identity'</span></span>
- <span data-ttu-id="f8009-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-232">'Razor'</span></span>
- <span data-ttu-id="f8009-233">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-234">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-235">'Blazor'</span></span>
- <span data-ttu-id="f8009-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-236">'Identity'</span></span>
- <span data-ttu-id="f8009-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-238">'Razor'</span></span>
- <span data-ttu-id="f8009-239">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-240">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-241">'Blazor'</span></span>
- <span data-ttu-id="f8009-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-242">'Identity'</span></span>
- <span data-ttu-id="f8009-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-244">'Razor'</span></span>
- <span data-ttu-id="f8009-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-246">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-247">'Blazor'</span></span>
- <span data-ttu-id="f8009-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-248">'Identity'</span></span>
- <span data-ttu-id="f8009-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-250">'Razor'</span></span>
- <span data-ttu-id="f8009-251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-252">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-253">'Blazor'</span></span>
- <span data-ttu-id="f8009-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-254">'Identity'</span></span>
- <span data-ttu-id="f8009-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-256">'Razor'</span></span>
- <span data-ttu-id="f8009-257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-258">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-259">'Blazor'</span></span>
- <span data-ttu-id="f8009-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-260">'Identity'</span></span>
- <span data-ttu-id="f8009-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-262">'Razor'</span></span>
- <span data-ttu-id="f8009-263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-264">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-265">'Blazor'</span></span>
- <span data-ttu-id="f8009-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-266">'Identity'</span></span>
- <span data-ttu-id="f8009-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-268">'Razor'</span></span>
- <span data-ttu-id="f8009-269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-270">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-271">'Blazor'</span></span>
- <span data-ttu-id="f8009-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-272">'Identity'</span></span>
- <span data-ttu-id="f8009-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-274">'Razor'</span></span>
- <span data-ttu-id="f8009-275">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-276">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-277">'Blazor'</span></span>
- <span data-ttu-id="f8009-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-278">'Identity'</span></span>
- <span data-ttu-id="f8009-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-280">'Razor'</span></span>
- <span data-ttu-id="f8009-281">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-282">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-283">'Blazor'</span></span>
- <span data-ttu-id="f8009-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-284">'Identity'</span></span>
- <span data-ttu-id="f8009-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-286">'Razor'</span></span>
- <span data-ttu-id="f8009-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-288">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-289">'Blazor'</span></span>
- <span data-ttu-id="f8009-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-290">'Identity'</span></span>
- <span data-ttu-id="f8009-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-292">'Razor'</span></span>
- <span data-ttu-id="f8009-293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-294">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-295">'Blazor'</span></span>
- <span data-ttu-id="f8009-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-296">'Identity'</span></span>
- <span data-ttu-id="f8009-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-298">'Razor'</span></span>
- <span data-ttu-id="f8009-299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-300">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-301">'Blazor'</span></span>
- <span data-ttu-id="f8009-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-302">'Identity'</span></span>
- <span data-ttu-id="f8009-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-304">'Razor'</span></span>
- <span data-ttu-id="f8009-305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-306">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-307">'Blazor'</span></span>
- <span data-ttu-id="f8009-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-308">'Identity'</span></span>
- <span data-ttu-id="f8009-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-310">'Razor'</span></span>
- <span data-ttu-id="f8009-311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-312">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-313">'Blazor'</span></span>
- <span data-ttu-id="f8009-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-314">'Identity'</span></span>
- <span data-ttu-id="f8009-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-316">'Razor'</span></span>
- <span data-ttu-id="f8009-317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-318">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-319">'Blazor'</span></span>
- <span data-ttu-id="f8009-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-320">'Identity'</span></span>
- <span data-ttu-id="f8009-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-322">'Razor'</span></span>
- <span data-ttu-id="f8009-323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-324">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-325">'Blazor'</span></span>
- <span data-ttu-id="f8009-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-326">'Identity'</span></span>
- <span data-ttu-id="f8009-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-328">'Razor'</span></span>
- <span data-ttu-id="f8009-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-330">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-331">'Blazor'</span></span>
- <span data-ttu-id="f8009-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-332">'Identity'</span></span>
- <span data-ttu-id="f8009-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-334">'Razor'</span></span>
- <span data-ttu-id="f8009-335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-336">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-337">'Blazor'</span></span>
- <span data-ttu-id="f8009-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-338">'Identity'</span></span>
- <span data-ttu-id="f8009-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-340">'Razor'</span></span>
- <span data-ttu-id="f8009-341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-342">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-343">'Blazor'</span></span>
- <span data-ttu-id="f8009-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-344">'Identity'</span></span>
- <span data-ttu-id="f8009-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-346">'Razor'</span></span>
- <span data-ttu-id="f8009-347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-348">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-349">'Blazor'</span></span>
- <span data-ttu-id="f8009-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-350">'Identity'</span></span>
- <span data-ttu-id="f8009-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-352">'Razor'</span></span>
- <span data-ttu-id="f8009-353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-354">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-355">'Blazor'</span></span>
- <span data-ttu-id="f8009-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-356">'Identity'</span></span>
- <span data-ttu-id="f8009-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-358">'Razor'</span></span>
- <span data-ttu-id="f8009-359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-360">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-361">'Blazor'</span></span>
- <span data-ttu-id="f8009-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-362">'Identity'</span></span>
- <span data-ttu-id="f8009-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-364">'Razor'</span></span>
- <span data-ttu-id="f8009-365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-366">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-367">'Blazor'</span></span>
- <span data-ttu-id="f8009-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-368">'Identity'</span></span>
- <span data-ttu-id="f8009-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-370">'Razor'</span></span>
- <span data-ttu-id="f8009-371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-372">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-373">'Blazor'</span></span>
- <span data-ttu-id="f8009-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-374">'Identity'</span></span>
- <span data-ttu-id="f8009-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-376">'Razor'</span></span>
- <span data-ttu-id="f8009-377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-378">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-379">'Blazor'</span></span>
- <span data-ttu-id="f8009-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-380">'Identity'</span></span>
- <span data-ttu-id="f8009-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-382">'Razor'</span></span>
- <span data-ttu-id="f8009-383">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-384">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-385">'Blazor'</span></span>
- <span data-ttu-id="f8009-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-386">'Identity'</span></span>
- <span data-ttu-id="f8009-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-388">'Razor'</span></span>
- <span data-ttu-id="f8009-389">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-390">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-391">'Blazor'</span></span>
- <span data-ttu-id="f8009-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-392">'Identity'</span></span>
- <span data-ttu-id="f8009-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-394">'Razor'</span></span>
- <span data-ttu-id="f8009-395">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-396">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-397">'Blazor'</span></span>
- <span data-ttu-id="f8009-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-398">'Identity'</span></span>
- <span data-ttu-id="f8009-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-400">'Razor'</span></span>
- <span data-ttu-id="f8009-401">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-402">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-403">'Blazor'</span></span>
- <span data-ttu-id="f8009-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-404">'Identity'</span></span>
- <span data-ttu-id="f8009-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-406">'Razor'</span></span>
- <span data-ttu-id="f8009-407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-408">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-409">'Blazor'</span></span>
- <span data-ttu-id="f8009-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-410">'Identity'</span></span>
- <span data-ttu-id="f8009-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-412">'Razor'</span></span>
- <span data-ttu-id="f8009-413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-414">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-415">'Blazor'</span></span>
- <span data-ttu-id="f8009-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-416">'Identity'</span></span>
- <span data-ttu-id="f8009-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-418">'Razor'</span></span>
- <span data-ttu-id="f8009-419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-420">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-421">'Blazor'</span></span>
- <span data-ttu-id="f8009-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-422">'Identity'</span></span>
- <span data-ttu-id="f8009-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-424">'Razor'</span></span>
- <span data-ttu-id="f8009-425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-426">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-427">'Blazor'</span></span>
- <span data-ttu-id="f8009-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-428">'Identity'</span></span>
- <span data-ttu-id="f8009-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-430">'Razor'</span></span>
- <span data-ttu-id="f8009-431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-432">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-433">'Blazor'</span></span>
- <span data-ttu-id="f8009-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-434">'Identity'</span></span>
- <span data-ttu-id="f8009-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-436">'Razor'</span></span>
- <span data-ttu-id="f8009-437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-438">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-439">'Blazor'</span></span>
- <span data-ttu-id="f8009-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-440">'Identity'</span></span>
- <span data-ttu-id="f8009-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-442">'Razor'</span></span>
- <span data-ttu-id="f8009-443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-443">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-444">---------------------------------------- | :---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-445">'Blazor'</span></span>
- <span data-ttu-id="f8009-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-446">'Identity'</span></span>
- <span data-ttu-id="f8009-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-448">'Razor'</span></span>
- <span data-ttu-id="f8009-449">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-450">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-451">'Blazor'</span></span>
- <span data-ttu-id="f8009-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-452">'Identity'</span></span>
- <span data-ttu-id="f8009-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-454">'Razor'</span></span>
- <span data-ttu-id="f8009-455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-456">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-457">'Blazor'</span></span>
- <span data-ttu-id="f8009-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-458">'Identity'</span></span>
- <span data-ttu-id="f8009-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-460">'Razor'</span></span>
- <span data-ttu-id="f8009-461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-462">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-463">'Blazor'</span></span>
- <span data-ttu-id="f8009-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-464">'Identity'</span></span>
- <span data-ttu-id="f8009-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-466">'Razor'</span></span>
- <span data-ttu-id="f8009-467">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-468">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-469">'Blazor'</span></span>
- <span data-ttu-id="f8009-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-470">'Identity'</span></span>
- <span data-ttu-id="f8009-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-472">'Razor'</span></span>
- <span data-ttu-id="f8009-473">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-474">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-475">'Blazor'</span></span>
- <span data-ttu-id="f8009-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-476">'Identity'</span></span>
- <span data-ttu-id="f8009-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-478">'Razor'</span></span>
- <span data-ttu-id="f8009-479">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-480">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-481">'Blazor'</span></span>
- <span data-ttu-id="f8009-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-482">'Identity'</span></span>
- <span data-ttu-id="f8009-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-484">'Razor'</span></span>
- <span data-ttu-id="f8009-485">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-486">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-487">'Blazor'</span></span>
- <span data-ttu-id="f8009-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-488">'Identity'</span></span>
- <span data-ttu-id="f8009-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-490">'Razor'</span></span>
- <span data-ttu-id="f8009-491">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-492">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-493">'Blazor'</span></span>
- <span data-ttu-id="f8009-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-494">'Identity'</span></span>
- <span data-ttu-id="f8009-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-496">'Razor'</span></span>
- <span data-ttu-id="f8009-497">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-498">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-499">'Blazor'</span></span>
- <span data-ttu-id="f8009-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-500">'Identity'</span></span>
- <span data-ttu-id="f8009-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-502">'Razor'</span></span>
- <span data-ttu-id="f8009-503">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-504">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-505">'Blazor'</span></span>
- <span data-ttu-id="f8009-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-506">'Identity'</span></span>
- <span data-ttu-id="f8009-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-508">'Razor'</span></span>
- <span data-ttu-id="f8009-509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-510">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-511">'Blazor'</span></span>
- <span data-ttu-id="f8009-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-512">'Identity'</span></span>
- <span data-ttu-id="f8009-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-514">'Razor'</span></span>
- <span data-ttu-id="f8009-515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-516">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-517">'Blazor'</span></span>
- <span data-ttu-id="f8009-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-518">'Identity'</span></span>
- <span data-ttu-id="f8009-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-520">'Razor'</span></span>
- <span data-ttu-id="f8009-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-521">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet |</span><span class="sxs-lookup"><span data-stu-id="f8009-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="f8009-523">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="f8009-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="f8009-524">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="f8009-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="f8009-525">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="f8009-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="f8009-526">BlazorSunucu uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="f8009-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="f8009-527">`.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="f8009-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="f8009-528">Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f8009-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="f8009-529">Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f8009-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="f8009-530">`"/{**path}"`Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f8009-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="f8009-531">Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .</span><span class="sxs-lookup"><span data-stu-id="f8009-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="f8009-532">`path`rota parametresi adı.</span><span class="sxs-lookup"><span data-stu-id="f8009-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="f8009-533">*Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8009-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="f8009-534">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f8009-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="f8009-535">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="f8009-535">NavLink component</span></span>

<span data-ttu-id="f8009-536"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="f8009-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="f8009-537">Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="f8009-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="f8009-538">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="f8009-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="f8009-539">Aşağıdaki `NavMenu` Bileşen, bileşenlerin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :</span><span class="sxs-lookup"><span data-stu-id="f8009-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="f8009-540"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="f8009-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="f8009-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>&ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Tüm geçerli URL ile eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="f8009-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType> &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="f8009-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*varsayılan*) &ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink>GEÇERLI URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="f8009-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*) &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="f8009-543">Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="f8009-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="f8009-544">İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="f8009-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="f8009-545">Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="f8009-546">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="f8009-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="f8009-547">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="f8009-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="f8009-548">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="f8009-548">URI and navigation state helpers</span></span>

<span data-ttu-id="f8009-549"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f8009-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="f8009-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f8009-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="f8009-551">Üye</span><span class="sxs-lookup"><span data-stu-id="f8009-551">Member</span></span> | <span data-ttu-id="f8009-552">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f8009-552">Description</span></span> |
| ---
<span data-ttu-id="f8009-553">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-554">'Blazor'</span></span>
- <span data-ttu-id="f8009-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-555">'Identity'</span></span>
- <span data-ttu-id="f8009-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-557">'Razor'</span></span>
- <span data-ttu-id="f8009-558">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-558">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-559">--- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-560">'Blazor'</span></span>
- <span data-ttu-id="f8009-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-561">'Identity'</span></span>
- <span data-ttu-id="f8009-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-563">'Razor'</span></span>
- <span data-ttu-id="f8009-564">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-565">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-566">'Blazor'</span></span>
- <span data-ttu-id="f8009-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-567">'Identity'</span></span>
- <span data-ttu-id="f8009-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-569">'Razor'</span></span>
- <span data-ttu-id="f8009-570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f8009-571">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f8009-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8009-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8009-572">'Blazor'</span></span>
- <span data-ttu-id="f8009-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8009-573">'Identity'</span></span>
- <span data-ttu-id="f8009-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8009-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8009-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8009-575">'Razor'</span></span>
- <span data-ttu-id="f8009-576">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f8009-576">'SignalR' uid:</span></span> 

<span data-ttu-id="f8009-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="f8009-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="f8009-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="f8009-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="f8009-579">Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` *Wwwroot/index.html* ( Blazor webassembly) veya *Pages/_Host. cshtml* (sunucu) içindeki belge öğesindeki özniteliğe karşılık gelir Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8009-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="f8009-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="f8009-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="f8009-581">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="f8009-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="f8009-582">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="f8009-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="f8009-583">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="f8009-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="f8009-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="f8009-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="f8009-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="f8009-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="f8009-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="f8009-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="f8009-587">Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="f8009-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="f8009-588">Aşağıdaki bileşen bir konum değiştirme olayını ayarıyla işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f8009-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f8009-589">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="f8009-590">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="f8009-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="f8009-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olayla ilgili aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="f8009-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="f8009-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Yeni konumun URL 'si.</span><span class="sxs-lookup"><span data-stu-id="f8009-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="f8009-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f8009-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="f8009-594">Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="f8009-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="f8009-595">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="f8009-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
