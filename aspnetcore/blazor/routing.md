---
<span data-ttu-id="36814-101">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-102">'Blazor'</span></span>
- <span data-ttu-id="36814-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-103">'Identity'</span></span>
- <span data-ttu-id="36814-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-105">'Razor'</span></span>
- <span data-ttu-id="36814-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="36814-107">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="36814-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="36814-108">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="36814-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="36814-109">İsteklerin nasıl yönlendirileceğini ve `NavLink` uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .</span><span class="sxs-lookup"><span data-stu-id="36814-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="36814-110">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36814-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="36814-111">Sunucu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="36814-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="36814-112">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde `MapBlazorHub` yapılandırılmıştır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="36814-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="36814-113">En yaygın yapılandırma, tüm isteklerin Razor sunucu uygulamasının sunucu tarafı bölümü için konak görevi gören bir sayfaya yönlendirilmesidir Blazor .</span><span class="sxs-lookup"><span data-stu-id="36814-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="36814-114">Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="36814-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="36814-115">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="36814-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="36814-116">Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="36814-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="36814-117">Bu, uygulamanın sunucu uygulamasını kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor .</span><span class="sxs-lookup"><span data-stu-id="36814-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="36814-118">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="36814-118">Route templates</span></span>

<span data-ttu-id="36814-119">`Router`Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="36814-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="36814-120">`Router`Bileşen *app. Razor* dosyasında görünür:</span><span class="sxs-lookup"><span data-stu-id="36814-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="36814-121">Bir yönergeyle bir *. Razor* dosyası `@page` derlendiğinde, oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="36814-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="36814-122">Çalışma zamanında, `RouteView` bileşen:</span><span class="sxs-lookup"><span data-stu-id="36814-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="36814-123">' İ `RouteData` `Router` istediğiniz parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="36814-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="36814-124">Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.</span><span class="sxs-lookup"><span data-stu-id="36814-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="36814-125">İsteğe bağlı olarak, `DefaultLayout` bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="36814-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="36814-126">Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` .</span><span class="sxs-lookup"><span data-stu-id="36814-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="36814-127">*Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="36814-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="36814-128">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="36814-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="36814-129">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="36814-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="36814-130">Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="36814-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="36814-131">URL 'Lerin doğru şekilde çözülmesi için, uygulamanın `<base>` *Wwwroot/index.html* dosyası ( Blazor webassembly) veya *Pages/_Host. cshtml* dosyasında ( Blazor sunucu), özniteliğinde belirtilen uygulama temel yolu ile bir etiket `href` ( `<base href="/">` ) içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="36814-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="36814-132">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="36814-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="36814-133">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="36814-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="36814-134">`Router`Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="36814-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="36814-135">*App. Razor* dosyasında, `NotFound` bileşenin Şablon parametresinde özel içerik ayarlayın `Router` :</span><span class="sxs-lookup"><span data-stu-id="36814-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="36814-136">`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="36814-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="36814-137">İçeriğe varsayılan bir düzen uygulamak için `NotFound` bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="36814-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="36814-138">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="36814-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="36814-139">`AdditionalAssemblies` `Router` Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="36814-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="36814-140">Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="36814-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="36814-141">Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="36814-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="36814-142">Aşağıdaki `AdditionalAssemblies` örnek, için yönlendirme desteği ile sonuçlanır `Component1` :</span><span class="sxs-lookup"><span data-stu-id="36814-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="36814-143">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="36814-143">Route parameters</span></span>

<span data-ttu-id="36814-144">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="36814-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="36814-145">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="36814-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="36814-146">`@page`Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="36814-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="36814-147">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="36814-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="36814-148">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="36814-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="36814-149">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="36814-149">Route constraints</span></span>

<span data-ttu-id="36814-150">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="36814-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="36814-151">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="36814-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="36814-152">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="36814-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="36814-153">`Id`Segment bir tamsayıdır ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="36814-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="36814-154">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="36814-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="36814-155">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="36814-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="36814-156">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="36814-156">Constraint</span></span> | <span data-ttu-id="36814-157">Örnek</span><span class="sxs-lookup"><span data-stu-id="36814-157">Example</span></span>           | <span data-ttu-id="36814-158">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="36814-158">Example Matches</span></span>                                                                  | <span data-ttu-id="36814-159">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="36814-159">Invariant</span></span><br><span data-ttu-id="36814-160">kültür</span><span class="sxs-lookup"><span data-stu-id="36814-160">culture</span></span><br><span data-ttu-id="36814-161">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="36814-161">matching</span></span> |
| ---
<span data-ttu-id="36814-162">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-163">'Blazor'</span></span>
- <span data-ttu-id="36814-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-164">'Identity'</span></span>
- <span data-ttu-id="36814-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-166">'Razor'</span></span>
- <span data-ttu-id="36814-167">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-168">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-169">'Blazor'</span></span>
- <span data-ttu-id="36814-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-170">'Identity'</span></span>
- <span data-ttu-id="36814-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-172">'Razor'</span></span>
- <span data-ttu-id="36814-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-174">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-175">'Blazor'</span></span>
- <span data-ttu-id="36814-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-176">'Identity'</span></span>
- <span data-ttu-id="36814-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-178">'Razor'</span></span>
- <span data-ttu-id="36814-179">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-179">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-180">----- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-181">'Blazor'</span></span>
- <span data-ttu-id="36814-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-182">'Identity'</span></span>
- <span data-ttu-id="36814-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-184">'Razor'</span></span>
- <span data-ttu-id="36814-185">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-186">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-187">'Blazor'</span></span>
- <span data-ttu-id="36814-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-188">'Identity'</span></span>
- <span data-ttu-id="36814-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-190">'Razor'</span></span>
- <span data-ttu-id="36814-191">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-192">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-193">'Blazor'</span></span>
- <span data-ttu-id="36814-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-194">'Identity'</span></span>
- <span data-ttu-id="36814-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-196">'Razor'</span></span>
- <span data-ttu-id="36814-197">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-198">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-199">'Blazor'</span></span>
- <span data-ttu-id="36814-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-200">'Identity'</span></span>
- <span data-ttu-id="36814-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-202">'Razor'</span></span>
- <span data-ttu-id="36814-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-204">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-205">'Blazor'</span></span>
- <span data-ttu-id="36814-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-206">'Identity'</span></span>
- <span data-ttu-id="36814-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-208">'Razor'</span></span>
- <span data-ttu-id="36814-209">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-210">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-211">'Blazor'</span></span>
- <span data-ttu-id="36814-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-212">'Identity'</span></span>
- <span data-ttu-id="36814-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-214">'Razor'</span></span>
- <span data-ttu-id="36814-215">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-215">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-216">--------- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-217">'Blazor'</span></span>
- <span data-ttu-id="36814-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-218">'Identity'</span></span>
- <span data-ttu-id="36814-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-220">'Razor'</span></span>
- <span data-ttu-id="36814-221">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-222">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-223">'Blazor'</span></span>
- <span data-ttu-id="36814-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-224">'Identity'</span></span>
- <span data-ttu-id="36814-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-226">'Razor'</span></span>
- <span data-ttu-id="36814-227">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-228">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-229">'Blazor'</span></span>
- <span data-ttu-id="36814-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-230">'Identity'</span></span>
- <span data-ttu-id="36814-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-232">'Razor'</span></span>
- <span data-ttu-id="36814-233">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-234">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-235">'Blazor'</span></span>
- <span data-ttu-id="36814-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-236">'Identity'</span></span>
- <span data-ttu-id="36814-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-238">'Razor'</span></span>
- <span data-ttu-id="36814-239">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-240">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-241">'Blazor'</span></span>
- <span data-ttu-id="36814-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-242">'Identity'</span></span>
- <span data-ttu-id="36814-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-244">'Razor'</span></span>
- <span data-ttu-id="36814-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-246">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-247">'Blazor'</span></span>
- <span data-ttu-id="36814-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-248">'Identity'</span></span>
- <span data-ttu-id="36814-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-250">'Razor'</span></span>
- <span data-ttu-id="36814-251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-252">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-253">'Blazor'</span></span>
- <span data-ttu-id="36814-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-254">'Identity'</span></span>
- <span data-ttu-id="36814-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-256">'Razor'</span></span>
- <span data-ttu-id="36814-257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-258">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-259">'Blazor'</span></span>
- <span data-ttu-id="36814-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-260">'Identity'</span></span>
- <span data-ttu-id="36814-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-262">'Razor'</span></span>
- <span data-ttu-id="36814-263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-264">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-265">'Blazor'</span></span>
- <span data-ttu-id="36814-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-266">'Identity'</span></span>
- <span data-ttu-id="36814-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-268">'Razor'</span></span>
- <span data-ttu-id="36814-269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-270">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-271">'Blazor'</span></span>
- <span data-ttu-id="36814-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-272">'Identity'</span></span>
- <span data-ttu-id="36814-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-274">'Razor'</span></span>
- <span data-ttu-id="36814-275">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-276">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-277">'Blazor'</span></span>
- <span data-ttu-id="36814-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-278">'Identity'</span></span>
- <span data-ttu-id="36814-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-280">'Razor'</span></span>
- <span data-ttu-id="36814-281">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-282">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-283">'Blazor'</span></span>
- <span data-ttu-id="36814-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-284">'Identity'</span></span>
- <span data-ttu-id="36814-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-286">'Razor'</span></span>
- <span data-ttu-id="36814-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-288">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-289">'Blazor'</span></span>
- <span data-ttu-id="36814-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-290">'Identity'</span></span>
- <span data-ttu-id="36814-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-292">'Razor'</span></span>
- <span data-ttu-id="36814-293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-294">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-295">'Blazor'</span></span>
- <span data-ttu-id="36814-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-296">'Identity'</span></span>
- <span data-ttu-id="36814-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-298">'Razor'</span></span>
- <span data-ttu-id="36814-299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-300">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-301">'Blazor'</span></span>
- <span data-ttu-id="36814-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-302">'Identity'</span></span>
- <span data-ttu-id="36814-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-304">'Razor'</span></span>
- <span data-ttu-id="36814-305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-306">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-307">'Blazor'</span></span>
- <span data-ttu-id="36814-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-308">'Identity'</span></span>
- <span data-ttu-id="36814-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-310">'Razor'</span></span>
- <span data-ttu-id="36814-311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-312">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-313">'Blazor'</span></span>
- <span data-ttu-id="36814-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-314">'Identity'</span></span>
- <span data-ttu-id="36814-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-316">'Razor'</span></span>
- <span data-ttu-id="36814-317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-318">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-319">'Blazor'</span></span>
- <span data-ttu-id="36814-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-320">'Identity'</span></span>
- <span data-ttu-id="36814-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-322">'Razor'</span></span>
- <span data-ttu-id="36814-323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-324">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-325">'Blazor'</span></span>
- <span data-ttu-id="36814-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-326">'Identity'</span></span>
- <span data-ttu-id="36814-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-328">'Razor'</span></span>
- <span data-ttu-id="36814-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-330">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-331">'Blazor'</span></span>
- <span data-ttu-id="36814-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-332">'Identity'</span></span>
- <span data-ttu-id="36814-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-334">'Razor'</span></span>
- <span data-ttu-id="36814-335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-336">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-337">'Blazor'</span></span>
- <span data-ttu-id="36814-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-338">'Identity'</span></span>
- <span data-ttu-id="36814-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-340">'Razor'</span></span>
- <span data-ttu-id="36814-341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-342">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-343">'Blazor'</span></span>
- <span data-ttu-id="36814-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-344">'Identity'</span></span>
- <span data-ttu-id="36814-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-346">'Razor'</span></span>
- <span data-ttu-id="36814-347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-348">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-349">'Blazor'</span></span>
- <span data-ttu-id="36814-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-350">'Identity'</span></span>
- <span data-ttu-id="36814-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-352">'Razor'</span></span>
- <span data-ttu-id="36814-353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-354">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-355">'Blazor'</span></span>
- <span data-ttu-id="36814-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-356">'Identity'</span></span>
- <span data-ttu-id="36814-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-358">'Razor'</span></span>
- <span data-ttu-id="36814-359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-360">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-361">'Blazor'</span></span>
- <span data-ttu-id="36814-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-362">'Identity'</span></span>
- <span data-ttu-id="36814-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-364">'Razor'</span></span>
- <span data-ttu-id="36814-365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-366">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-367">'Blazor'</span></span>
- <span data-ttu-id="36814-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-368">'Identity'</span></span>
- <span data-ttu-id="36814-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-370">'Razor'</span></span>
- <span data-ttu-id="36814-371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-372">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-373">'Blazor'</span></span>
- <span data-ttu-id="36814-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-374">'Identity'</span></span>
- <span data-ttu-id="36814-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-376">'Razor'</span></span>
- <span data-ttu-id="36814-377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-378">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-379">'Blazor'</span></span>
- <span data-ttu-id="36814-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-380">'Identity'</span></span>
- <span data-ttu-id="36814-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-382">'Razor'</span></span>
- <span data-ttu-id="36814-383">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-384">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-385">'Blazor'</span></span>
- <span data-ttu-id="36814-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-386">'Identity'</span></span>
- <span data-ttu-id="36814-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-388">'Razor'</span></span>
- <span data-ttu-id="36814-389">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-390">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-391">'Blazor'</span></span>
- <span data-ttu-id="36814-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-392">'Identity'</span></span>
- <span data-ttu-id="36814-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-394">'Razor'</span></span>
- <span data-ttu-id="36814-395">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-396">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-397">'Blazor'</span></span>
- <span data-ttu-id="36814-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-398">'Identity'</span></span>
- <span data-ttu-id="36814-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-400">'Razor'</span></span>
- <span data-ttu-id="36814-401">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-402">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-403">'Blazor'</span></span>
- <span data-ttu-id="36814-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-404">'Identity'</span></span>
- <span data-ttu-id="36814-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-406">'Razor'</span></span>
- <span data-ttu-id="36814-407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-408">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-409">'Blazor'</span></span>
- <span data-ttu-id="36814-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-410">'Identity'</span></span>
- <span data-ttu-id="36814-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-412">'Razor'</span></span>
- <span data-ttu-id="36814-413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-414">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-415">'Blazor'</span></span>
- <span data-ttu-id="36814-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-416">'Identity'</span></span>
- <span data-ttu-id="36814-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-418">'Razor'</span></span>
- <span data-ttu-id="36814-419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-420">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-421">'Blazor'</span></span>
- <span data-ttu-id="36814-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-422">'Identity'</span></span>
- <span data-ttu-id="36814-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-424">'Razor'</span></span>
- <span data-ttu-id="36814-425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-426">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-427">'Blazor'</span></span>
- <span data-ttu-id="36814-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-428">'Identity'</span></span>
- <span data-ttu-id="36814-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-430">'Razor'</span></span>
- <span data-ttu-id="36814-431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-432">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-433">'Blazor'</span></span>
- <span data-ttu-id="36814-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-434">'Identity'</span></span>
- <span data-ttu-id="36814-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-436">'Razor'</span></span>
- <span data-ttu-id="36814-437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-438">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-439">'Blazor'</span></span>
- <span data-ttu-id="36814-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-440">'Identity'</span></span>
- <span data-ttu-id="36814-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-442">'Razor'</span></span>
- <span data-ttu-id="36814-443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-443">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-444">---------------------------------------- | :---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-445">'Blazor'</span></span>
- <span data-ttu-id="36814-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-446">'Identity'</span></span>
- <span data-ttu-id="36814-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-448">'Razor'</span></span>
- <span data-ttu-id="36814-449">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-450">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-451">'Blazor'</span></span>
- <span data-ttu-id="36814-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-452">'Identity'</span></span>
- <span data-ttu-id="36814-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-454">'Razor'</span></span>
- <span data-ttu-id="36814-455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-456">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-457">'Blazor'</span></span>
- <span data-ttu-id="36814-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-458">'Identity'</span></span>
- <span data-ttu-id="36814-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-460">'Razor'</span></span>
- <span data-ttu-id="36814-461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-462">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-463">'Blazor'</span></span>
- <span data-ttu-id="36814-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-464">'Identity'</span></span>
- <span data-ttu-id="36814-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-466">'Razor'</span></span>
- <span data-ttu-id="36814-467">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-468">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-469">'Blazor'</span></span>
- <span data-ttu-id="36814-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-470">'Identity'</span></span>
- <span data-ttu-id="36814-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-472">'Razor'</span></span>
- <span data-ttu-id="36814-473">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-474">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-475">'Blazor'</span></span>
- <span data-ttu-id="36814-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-476">'Identity'</span></span>
- <span data-ttu-id="36814-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-478">'Razor'</span></span>
- <span data-ttu-id="36814-479">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-480">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-481">'Blazor'</span></span>
- <span data-ttu-id="36814-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-482">'Identity'</span></span>
- <span data-ttu-id="36814-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-484">'Razor'</span></span>
- <span data-ttu-id="36814-485">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-486">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-487">'Blazor'</span></span>
- <span data-ttu-id="36814-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-488">'Identity'</span></span>
- <span data-ttu-id="36814-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-490">'Razor'</span></span>
- <span data-ttu-id="36814-491">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-492">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-493">'Blazor'</span></span>
- <span data-ttu-id="36814-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-494">'Identity'</span></span>
- <span data-ttu-id="36814-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-496">'Razor'</span></span>
- <span data-ttu-id="36814-497">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-498">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-499">'Blazor'</span></span>
- <span data-ttu-id="36814-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-500">'Identity'</span></span>
- <span data-ttu-id="36814-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-502">'Razor'</span></span>
- <span data-ttu-id="36814-503">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-504">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-505">'Blazor'</span></span>
- <span data-ttu-id="36814-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-506">'Identity'</span></span>
- <span data-ttu-id="36814-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-508">'Razor'</span></span>
- <span data-ttu-id="36814-509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-510">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-511">'Blazor'</span></span>
- <span data-ttu-id="36814-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-512">'Identity'</span></span>
- <span data-ttu-id="36814-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-514">'Razor'</span></span>
- <span data-ttu-id="36814-515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-516">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-517">'Blazor'</span></span>
- <span data-ttu-id="36814-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-518">'Identity'</span></span>
- <span data-ttu-id="36814-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-520">'Razor'</span></span>
- <span data-ttu-id="36814-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-521">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet |</span><span class="sxs-lookup"><span data-stu-id="36814-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="36814-523">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` `DateTime` , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="36814-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="36814-524">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="36814-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="36814-525">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="36814-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="36814-526">BlazorSunucu uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="36814-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="36814-527">`.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="36814-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="36814-528">Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="36814-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="36814-529">Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="36814-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="36814-530">`"/{**path}"`Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="36814-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="36814-531">Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .</span><span class="sxs-lookup"><span data-stu-id="36814-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="36814-532">`path`rota parametresi adı.</span><span class="sxs-lookup"><span data-stu-id="36814-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="36814-533">*Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="36814-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="36814-534">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="36814-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="36814-535">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="36814-535">NavLink component</span></span>

<span data-ttu-id="36814-536">`NavLink`Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="36814-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="36814-537">Bir `NavLink` bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="36814-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="36814-538">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="36814-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="36814-539">Aşağıdaki `NavMenu` Bileşen, bileşenlerin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur `NavLink` :</span><span class="sxs-lookup"><span data-stu-id="36814-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="36814-540">`NavLinkMatch`Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="36814-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="36814-541">`NavLinkMatch.All`&ndash; `NavLink` Tüm geçerli URL ile eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="36814-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="36814-542">`NavLinkMatch.Prefix`(*varsayılan*) &ndash; `NavLink`GEÇERLI URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="36814-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="36814-543">Yukarıdaki örnekte, ana giriş URL 'siyle `NavLink` `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="36814-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="36814-544">İkincisi, `NavLink` `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="36814-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="36814-545">Ek `NavLink` bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="36814-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="36814-546">Aşağıdaki örnekte, `NavLink` bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="36814-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="36814-547">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="36814-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="36814-548">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="36814-548">URI and navigation state helpers</span></span>

<span data-ttu-id="36814-549"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="36814-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="36814-550">`NavigationManager`Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="36814-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="36814-551">Üye</span><span class="sxs-lookup"><span data-stu-id="36814-551">Member</span></span> | <span data-ttu-id="36814-552">Açıklama</span><span class="sxs-lookup"><span data-stu-id="36814-552">Description</span></span> |
| ---
<span data-ttu-id="36814-553">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-554">'Blazor'</span></span>
- <span data-ttu-id="36814-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-555">'Identity'</span></span>
- <span data-ttu-id="36814-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-557">'Razor'</span></span>
- <span data-ttu-id="36814-558">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-558">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-559">--- | ---title: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-560">'Blazor'</span></span>
- <span data-ttu-id="36814-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-561">'Identity'</span></span>
- <span data-ttu-id="36814-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-563">'Razor'</span></span>
- <span data-ttu-id="36814-564">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-565">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-566">'Blazor'</span></span>
- <span data-ttu-id="36814-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-567">'Identity'</span></span>
- <span data-ttu-id="36814-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-569">'Razor'</span></span>
- <span data-ttu-id="36814-570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="36814-571">Başlık: ' ASP.NET Core Blazor Yönlendirme ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="36814-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36814-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36814-572">'Blazor'</span></span>
- <span data-ttu-id="36814-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36814-573">'Identity'</span></span>
- <span data-ttu-id="36814-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36814-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="36814-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36814-575">'Razor'</span></span>
- <span data-ttu-id="36814-576">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="36814-576">'SignalR' uid:</span></span> 

<span data-ttu-id="36814-577">------ | | URI | Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="36814-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="36814-578">| | BaseUri | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="36814-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="36814-579">Genellikle, `BaseUri` `href` `<base>` *Wwwroot/index.html* ( Blazor webassembly) veya *Pages/_Host. cshtml* (sunucu) içindeki belge öğesindeki özniteliğe karşılık gelir Blazor .</span><span class="sxs-lookup"><span data-stu-id="36814-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="36814-580">| | NavigateTo | Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="36814-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="36814-581">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="36814-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="36814-582">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="36814-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="36814-583">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="36814-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="36814-584">| | LocationChanged | Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="36814-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="36814-585">| | ToAbsoluteUri | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="36814-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="36814-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde `GetBaseUri` , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="36814-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="36814-587">Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="36814-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="36814-588">Aşağıdaki bileşen bir konum değişti olayını işler.</span><span class="sxs-lookup"><span data-stu-id="36814-588">The following component handles a location changed event.</span></span> <span data-ttu-id="36814-589">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="36814-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="36814-590">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="36814-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="36814-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olayla ilgili aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="36814-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="36814-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Yeni konumun URL 'si.</span><span class="sxs-lookup"><span data-stu-id="36814-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="36814-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="36814-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="36814-594">İse `false` , [navigationmanager. Navigate,](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="36814-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="36814-595">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="36814-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
