---
title: ASP.NET Core Blazor Yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 9668077d9b59ff20b1aab0b496278f2460e5ad2a
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103864"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="e713c-103">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="e713c-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="e713c-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="e713c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e713c-105">İsteklerin nasıl yönlendirileceğini ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .</span><span class="sxs-lookup"><span data-stu-id="e713c-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="e713c-106">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e713c-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="e713c-107">Sunucu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="e713c-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="e713c-108">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e713c-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="e713c-109">En yaygın yapılandırma, tüm isteklerin Razor sunucu uygulamasının sunucu tarafı bölümü için konak görevi gören bir sayfaya yönlendirilmesidir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e713c-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="e713c-110">Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="e713c-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="e713c-111">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="e713c-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="e713c-112">Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="e713c-113">Bu, uygulamanın sunucu uygulamasını kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e713c-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="e713c-114">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="e713c-114">Route templates</span></span>

<span data-ttu-id="e713c-115"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e713c-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="e713c-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen *app. Razor* dosyasında görünür:</span><span class="sxs-lookup"><span data-stu-id="e713c-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="e713c-117">Bir yönergeyle bir *. Razor* dosyası `@page` derlendiğinde, oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e713c-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="e713c-118">Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:</span><span class="sxs-lookup"><span data-stu-id="e713c-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="e713c-119">' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> istediğiniz parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="e713c-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="e713c-120">Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.</span><span class="sxs-lookup"><span data-stu-id="e713c-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="e713c-121">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e713c-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="e713c-122">Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` .</span><span class="sxs-lookup"><span data-stu-id="e713c-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="e713c-123">*Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="e713c-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="e713c-124">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="e713c-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="e713c-125">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="e713c-126">Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="e713c-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="e713c-127">URL 'Lerin doğru şekilde çözülmesi için, uygulamanın `<base>` *wwwroot/index.html* dosyası ( Blazor Webassembly) veya *Pages/_Host. cshtml* dosyasında ( Blazor sunucu) () özniteliğinde belirtilen uygulama temel yolu ile bir etiket içermesi gerekir `href` `<base href="/">` .</span><span class="sxs-lookup"><span data-stu-id="e713c-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="e713c-128">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="e713c-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="e713c-129">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="e713c-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="e713c-130"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e713c-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="e713c-131">*App. Razor* dosyasında, <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bileşenin Şablon parametresinde özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> :</span><span class="sxs-lookup"><span data-stu-id="e713c-131">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="e713c-132">`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="e713c-133">İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="e713c-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="e713c-134">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="e713c-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="e713c-135"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e713c-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="e713c-136">Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="e713c-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="e713c-137">Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="e713c-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="e713c-138">Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteği ile sonuçlanır `Component1` :</span><span class="sxs-lookup"><span data-stu-id="e713c-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="e713c-139">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="e713c-139">Route parameters</span></span>

<span data-ttu-id="e713c-140">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="e713c-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="e713c-141">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e713c-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="e713c-142">`@page`Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e713c-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="e713c-143">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="e713c-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="e713c-144">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="e713c-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="e713c-145">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="e713c-145">Route constraints</span></span>

<span data-ttu-id="e713c-146">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="e713c-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="e713c-147">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="e713c-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="e713c-148">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="e713c-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="e713c-149">`Id`Segment bir tamsayıdır ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="e713c-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="e713c-150">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="e713c-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="e713c-151">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="e713c-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="e713c-152">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="e713c-152">Constraint</span></span> | <span data-ttu-id="e713c-153">Örnek</span><span class="sxs-lookup"><span data-stu-id="e713c-153">Example</span></span>           | <span data-ttu-id="e713c-154">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="e713c-154">Example Matches</span></span>                                                                  | <span data-ttu-id="e713c-155">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="e713c-155">Invariant</span></span><br><span data-ttu-id="e713c-156">kültür</span><span class="sxs-lookup"><span data-stu-id="e713c-156">culture</span></span><br><span data-ttu-id="e713c-157">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="e713c-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="e713c-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="e713c-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="e713c-159">Hayır</span><span class="sxs-lookup"><span data-stu-id="e713c-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="e713c-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="e713c-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="e713c-161">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="e713c-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="e713c-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="e713c-163">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="e713c-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e713c-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e713c-165">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="e713c-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e713c-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e713c-167">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="e713c-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="e713c-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="e713c-169">Hayır</span><span class="sxs-lookup"><span data-stu-id="e713c-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="e713c-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e713c-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e713c-171">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="e713c-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e713c-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e713c-173">Yes</span><span class="sxs-lookup"><span data-stu-id="e713c-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="e713c-174">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="e713c-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="e713c-175">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="e713c-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="e713c-176">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="e713c-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="e713c-177">BlazorSunucu uygulamalarında *_Host. cshtml* içindeki varsayılan yol `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="e713c-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="e713c-178">`.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="e713c-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="e713c-179">Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="e713c-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="e713c-180">Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="e713c-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="e713c-181">`"/{**path}"`Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e713c-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="e713c-182">Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .</span><span class="sxs-lookup"><span data-stu-id="e713c-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="e713c-183">`path`rota parametresi adı.</span><span class="sxs-lookup"><span data-stu-id="e713c-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="e713c-184">*Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e713c-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="e713c-185">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="e713c-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="e713c-186">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="e713c-186">NavLink component</span></span>

<span data-ttu-id="e713c-187"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="e713c-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="e713c-188">Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="e713c-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="e713c-189">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="e713c-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="e713c-190">Aşağıdaki `NavMenu` Bileşen, bileşenlerin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :</span><span class="sxs-lookup"><span data-stu-id="e713c-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="e713c-191"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="e713c-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="e713c-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> GEÇERLI URL 'nin tamamı eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="e713c-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="e713c-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*varsayılan*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="e713c-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="e713c-194">Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="e713c-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="e713c-195">İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="e713c-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="e713c-196">Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="e713c-197">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="e713c-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="e713c-198">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="e713c-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="e713c-199">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="e713c-199">URI and navigation state helpers</span></span>

<span data-ttu-id="e713c-200"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="e713c-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="e713c-201"><xref:Microsoft.AspNetCore.Components.NavigationManager>Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="e713c-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="e713c-202">Üye</span><span class="sxs-lookup"><span data-stu-id="e713c-202">Member</span></span> | <span data-ttu-id="e713c-203">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e713c-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="e713c-204">Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="e713c-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="e713c-205">Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="e713c-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="e713c-206">Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` *Wwwroot/index.html* ( Blazor webassembly) veya *Pages/_Host. cshtml* (Server) içindeki belge öğesindeki özniteliğe karşılık gelir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e713c-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="e713c-207">Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="e713c-207">Navigates to the specified URI.</span></span> <span data-ttu-id="e713c-208">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="e713c-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="e713c-209">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="e713c-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="e713c-210">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="e713c-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="e713c-211">Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="e713c-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="e713c-212">Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="e713c-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="e713c-213">Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="e713c-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="e713c-214">Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="e713c-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="e713c-215">Aşağıdaki bileşen bir konum değiştirme olayını ayarıyla işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e713c-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e713c-216">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="e713c-217">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e713c-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="e713c-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olayla ilgili aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="e713c-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="e713c-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Yeni konumun URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="e713c-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="e713c-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e713c-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="e713c-221">Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="e713c-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="e713c-222">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="e713c-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
