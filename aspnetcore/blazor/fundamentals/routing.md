---
title: ASP.NET Core Blazor Yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
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
ms.openlocfilehash: 0c878a05a50e5a6879278ee737ada167669ee0ff
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626486"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="6a97c-103">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6a97c-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="6a97c-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="6a97c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6a97c-105">İsteklerin nasıl yönlendirileceğini ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağını öğrenin Blazor .</span><span class="sxs-lookup"><span data-stu-id="6a97c-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="6a97c-106">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a97c-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="6a97c-107">Blazor Server[ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6a97c-108">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="6a97c-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6a97c-109">En yaygın yapılandırma, tüm istekleri bir sayfaya yönlendirmesidir Razor ve bu, uygulamanın sunucu tarafı bölümü için ana bilgisayar işlevi görür Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="6a97c-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="6a97c-110">Kurala göre, *ana bilgisayar* sayfası genellikle adlandırılır `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="6a97c-111">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="6a97c-112">Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="6a97c-113">Bu, uygulamanın uygulamayı kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="6a97c-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="6a97c-114"><xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>Kök olmayan URL sunucusu barındırma için yapılandırma hakkında bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="6a97c-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="6a97c-115">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="6a97c-115">Route templates</span></span>

<span data-ttu-id="6a97c-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="6a97c-117"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen `App.razor` dosyada görünür:</span><span class="sxs-lookup"><span data-stu-id="6a97c-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="6a97c-118">`.razor` `@page` Yönergeyle bir dosya derlendiğinde oluşturulan sınıf, <xref:Microsoft.AspNetCore.Components.RouteAttribute> yol şablonunu belirten bir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="6a97c-119">Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:</span><span class="sxs-lookup"><span data-stu-id="6a97c-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="6a97c-120">' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> istediğiniz parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="6a97c-121">Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.</span><span class="sxs-lookup"><span data-stu-id="6a97c-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="6a97c-122">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> bir düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6a97c-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="6a97c-123">Varsayılan Blazor Şablonlar, bileşeni belirtir `MainLayout` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="6a97c-124">`MainLayout.razor` Şablon projenin `Shared` klasörü.</span><span class="sxs-lookup"><span data-stu-id="6a97c-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="6a97c-125">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="6a97c-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="6a97c-126">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6a97c-127">Aşağıdaki bileşen ve için isteklere yanıt verir `/BlazorRoute` `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="6a97c-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="6a97c-128">URL 'Lerin doğru bir şekilde çözülmesi için, uygulamanın, () `<base>` `wwwroot/index.html` Blazor WebAssembly `Pages/_Host.cshtml` Blazor Server özniteliğinde belirtilen uygulama temel yolu ile dosyasında () veya dosyasında ( `href` `<base href="/">` ) bir etiket içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="6a97c-129">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6a97c-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="6a97c-130">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="6a97c-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="6a97c-131"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="6a97c-132">`App.razor`Dosyasında, <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bileşenin Şablon parametresinde özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> :</span><span class="sxs-lookup"><span data-stu-id="6a97c-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="6a97c-133">`<NotFound>`Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="6a97c-134">İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="6a97c-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="6a97c-135">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="6a97c-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="6a97c-136"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="6a97c-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="6a97c-137">Belirtilen derlemeler, belirtilen derlemeye ek olarak değerlendirilir `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="6a97c-138">Aşağıdaki örnekte, başvurulan bir `Component1` sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="6a97c-139">Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteği ile sonuçlanır `Component1` :</span><span class="sxs-lookup"><span data-stu-id="6a97c-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="6a97c-140">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="6a97c-140">Route parameters</span></span>

<span data-ttu-id="6a97c-141">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="6a97c-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="6a97c-142">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="6a97c-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="6a97c-143">`@page`Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="6a97c-144">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6a97c-145">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="6a97c-146">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="6a97c-146">Route constraints</span></span>

<span data-ttu-id="6a97c-147">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="6a97c-148">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="6a97c-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="6a97c-149">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="6a97c-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="6a97c-150">`Id`Segment bir tamsayıdır ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="6a97c-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="6a97c-151">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="6a97c-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="6a97c-152">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="6a97c-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="6a97c-153">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="6a97c-153">Constraint</span></span> | <span data-ttu-id="6a97c-154">Örnek</span><span class="sxs-lookup"><span data-stu-id="6a97c-154">Example</span></span>           | <span data-ttu-id="6a97c-155">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="6a97c-155">Example Matches</span></span>                                                                  | <span data-ttu-id="6a97c-156">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="6a97c-156">Invariant</span></span><br><span data-ttu-id="6a97c-157">kültür</span><span class="sxs-lookup"><span data-stu-id="6a97c-157">culture</span></span><br><span data-ttu-id="6a97c-158">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="6a97c-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="6a97c-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="6a97c-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="6a97c-160">No</span><span class="sxs-lookup"><span data-stu-id="6a97c-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="6a97c-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="6a97c-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="6a97c-162">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="6a97c-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="6a97c-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="6a97c-164">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="6a97c-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6a97c-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6a97c-166">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="6a97c-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6a97c-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6a97c-168">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="6a97c-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="6a97c-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="6a97c-170">No</span><span class="sxs-lookup"><span data-stu-id="6a97c-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="6a97c-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6a97c-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6a97c-172">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="6a97c-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6a97c-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6a97c-174">Yes</span><span class="sxs-lookup"><span data-stu-id="6a97c-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="6a97c-175">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="6a97c-176">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="6a97c-177">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6a97c-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="6a97c-178">Blazor ServerUygulamalarda varsayılan yol `_Host.cshtml` `/` () ' dir `@page "/"` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-178">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="6a97c-179">`.`URL bir dosya isteyecek şekilde göründüğünden, nokta () içeren bir Istek URL 'si varsayılan yol tarafından eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6a97c-179">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="6a97c-180">Bir Blazor uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="6a97c-180">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="6a97c-181">Bir nokta içeren yolları kullanmak için `_Host.cshtml` Aşağıdaki rota şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="6a97c-181">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="6a97c-182">`"/{**path}"`Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="6a97c-182">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="6a97c-183">Ters *catch-all* `**` eğik çizgi () kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız catch-all söz dizimi () `/` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-183">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="6a97c-184">`path` rota parametresi adı.</span><span class="sxs-lookup"><span data-stu-id="6a97c-184">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="6a97c-185">*Catch-all* parametre sözdizimi ( `*` / `**` ) **not** Razor bileşenlerde ( `.razor` ) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="6a97c-185">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="6a97c-186">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="6a97c-186">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="6a97c-187">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="6a97c-187">NavLink component</span></span>

<span data-ttu-id="6a97c-188"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-188">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="6a97c-189">Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-189">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="6a97c-190">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="6a97c-190">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="6a97c-191">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> geçerli yol ile eşleştiğinde işlenen bağlantıya özel bır CSS sınıfı uygulamak için BIR CSS sınıfı adı atayın `href` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-191">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="6a97c-192">Aşağıdaki `NavMenu` Bileşen, [`Bootstrap`](https://getbootstrap.com/docs/) bileşenlerin nasıl kullanılacağını gösteren bir gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :</span><span class="sxs-lookup"><span data-stu-id="6a97c-192">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="6a97c-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="6a97c-193">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="6a97c-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> GEÇERLI URL 'nin tamamı eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="6a97c-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="6a97c-195"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*varsayılan*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="6a97c-195"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="6a97c-196">Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="6a97c-196">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="6a97c-197">İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `MyComponent` (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="6a97c-197">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="6a97c-198">Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-198">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="6a97c-199">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="6a97c-199">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="6a97c-200">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="6a97c-200">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="6a97c-201">BlazorAlt içeriği işleyen bir yol nedeniyle, `NavLink` bir döngü içindeki işleme bileşenleri, `for` `NavLink` (alt) bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa bir yerel dizin değişkeni gerektirir:</span><span class="sxs-lookup"><span data-stu-id="6a97c-201">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="6a97c-202">Bu senaryoda bir dizin değişkeni kullanılması yalnızca bileşeni değil, [alt içeriğinde](xref:blazor/components/index#child-content)bir döngü değişkeni kullanan **herhangi** bir alt bileşen için gereksinimdir `NavLink` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-202">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="6a97c-203">Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="6a97c-203">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="6a97c-204">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="6a97c-204">URI and navigation state helpers</span></span>

<span data-ttu-id="6a97c-205"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6a97c-205">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="6a97c-206"><xref:Microsoft.AspNetCore.Components.NavigationManager> Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="6a97c-206"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="6a97c-207">Üye</span><span class="sxs-lookup"><span data-stu-id="6a97c-207">Member</span></span> | <span data-ttu-id="6a97c-208">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6a97c-208">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="6a97c-209">Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-209">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="6a97c-210">Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-210">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="6a97c-211">Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` `wwwroot/index.html` ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` () içinde belge öğesindeki özniteliğine karşılık gelir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="6a97c-211">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="6a97c-212">Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="6a97c-212">Navigates to the specified URI.</span></span> <span data-ttu-id="6a97c-213">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="6a97c-213">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="6a97c-214">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-214">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="6a97c-215">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-215">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="6a97c-216">Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="6a97c-216">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="6a97c-217">Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="6a97c-217">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="6a97c-218">Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="6a97c-218">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="6a97c-219">Aşağıdaki bileşen, `Counter` Düğme seçildiğinde uygulamanın bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="6a97c-219">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="6a97c-220">Aşağıdaki bileşen öğesine abone olunarak bir konum değiştirme olayını işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6a97c-220">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6a97c-221">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-221">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="6a97c-222">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-222">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="6a97c-223"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> olayla ilgili aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="6a97c-223"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="6a97c-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Yeni konumun URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="6a97c-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="6a97c-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="6a97c-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="6a97c-226">Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="6a97c-226">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="6a97c-227">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="6a97c-227">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="6a97c-228">Sorgu dizesi ve ayrıştırma parametreleri</span><span class="sxs-lookup"><span data-stu-id="6a97c-228">Query string and parse parameters</span></span>

<span data-ttu-id="6a97c-229">Bir isteğin sorgu dizesi özelliğinden elde edilebilir <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> :</span><span class="sxs-lookup"><span data-stu-id="6a97c-229">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="6a97c-230">Bir sorgu dizesinin parametrelerini ayrıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="6a97c-230">To parse a query string's parameters:</span></span>

* <span data-ttu-id="6a97c-231">[Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)için bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6a97c-231">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="6a97c-232">İle sorgu dizesini ayrıştırdıktan sonra değeri alın <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6a97c-232">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="6a97c-233">`{KEY}`Önceki örnekteki yer tutucu sorgu dizesi parametre anahtarıdır.</span><span class="sxs-lookup"><span data-stu-id="6a97c-233">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="6a97c-234">Örneğin, URL anahtar-değer çifti, `?ship=Tardis` bir anahtarını kullanır `ship` .</span><span class="sxs-lookup"><span data-stu-id="6a97c-234">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
