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
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="b8f86-103">ASP.NET Core Blazor Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="b8f86-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="b8f86-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="b8f86-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8f86-105">Bu makalede, istek yönlendirmeyi yönetme ve <xref:Microsoft.AspNetCore.Components.Routing.NavLink> uygulamalarda gezinti bağlantıları oluşturmak için bileşenin nasıl kullanılacağı hakkında bilgi edinin Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8f86-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="b8f86-106">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="b8f86-106">Route templates</span></span>

<span data-ttu-id="b8f86-107"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, Razor bir uygulamadaki bileşenlere yönlendirmeyi sağlar Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8f86-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="b8f86-108"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, `App` uygulamalar bileşeninde kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8f86-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="b8f86-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="b8f86-110">Razor `.razor` [ `@page` Yönergesi](xref:mvc/views/razor#page) içeren bir bileşen () derlendiğinde, oluşturulan bileşen sınıfı, <xref:Microsoft.AspNetCore.Components.RouteAttribute> bileşenin yol şablonunu belirten bir olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="b8f86-111">Uygulama başlatıldığında, yönlendirici olarak belirtilen derleme, `AppAssembly` uygulamanın bir öğesine sahip olan bileşenleri için rota bilgilerini toplayacak şekilde taranır <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="b8f86-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="b8f86-112">Çalışma zamanında, <xref:Microsoft.AspNetCore.Components.RouteView> bileşen:</span><span class="sxs-lookup"><span data-stu-id="b8f86-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="b8f86-113">' İ <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> herhangi bir rota parametresiyle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="b8f86-114">Daha fazla iç içe yerleştirilmiş düzen dahil olmak üzere, belirtilen bileşeni [düzeniyle](xref:blazor/layouts)işler.</span><span class="sxs-lookup"><span data-stu-id="b8f86-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="b8f86-115">İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> , [ `@layout` yönergeyle](xref:blazor/layouts#specify-a-layout-in-a-component)bir düzen belirtmeyen bileşenler için Düzen sınıfı ile bir parametre belirtin.</span><span class="sxs-lookup"><span data-stu-id="b8f86-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="b8f86-116">Çerçevenin Blazor Proje şablonları, `MainLayout` `Shared/MainLayout.razor` uygulamanın varsayılan düzeni olarak bileşenini () belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="b8f86-117">Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts> ..</span><span class="sxs-lookup"><span data-stu-id="b8f86-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="b8f86-118">Bileşenler birden çok [ `@page` yönergeleri](xref:mvc/views/razor#page)kullanarak birden çok yol şablonunu destekler.</span><span class="sxs-lookup"><span data-stu-id="b8f86-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="b8f86-119">Aşağıdaki örnek bileşen ve için istekleri yükler `/BlazorRoute` `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="b8f86-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="b8f86-121">URL 'Lerin doğru çözümlenmesi için, uygulamanın, `<base>` `wwwroot/index.html` Blazor WebAssembly `Pages/_Host.cshtml` Blazor Server özniteliğinde belirtilen uygulama temel yolu ile dosyasında () veya dosyasında () bir etiket içermesi gerekir `href` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="b8f86-122">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b8f86-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="b8f86-123">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="b8f86-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="b8f86-124"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="b8f86-125">`App`Bileşende bileşen şablonunda özel içerik ayarlayın <xref:Microsoft.AspNetCore.Components.Routing.Router> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="b8f86-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="b8f86-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="b8f86-127">Diğer etkileşimli bileşenler gibi etiketlerin içeriği olarak rastgele öğeler desteklenir `<NotFound>` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="b8f86-128">İçeriğe varsayılan bir düzen uygulamak için <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bkz <xref:blazor/layouts#default-layout> ..</span><span class="sxs-lookup"><span data-stu-id="b8f86-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="b8f86-129">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="b8f86-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="b8f86-130"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> <xref:Microsoft.AspNetCore.Components.Routing.Router> Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8f86-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="b8f86-131">Ek derlemeler, için belirtilen derlemeye ek olarak taranır `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="b8f86-132">Aşağıdaki örnekte, `Component1` başvurulan [bileşen sınıfı kitaplığında](xref:blazor/components/class-libraries)tanımlanan yönlendirilebilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="b8f86-133">Aşağıdaki <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> örnek, için yönlendirme desteğine neden olur `Component1` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="b8f86-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="b8f86-135">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="b8f86-135">Route parameters</span></span>

<span data-ttu-id="b8f86-136">Yönlendirici, karşılık gelen [bileşen parametrelerini](xref:blazor/components/index#component-parameters) aynı adla doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="b8f86-137">Yol parametresi adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="b8f86-138">Aşağıdaki örnekte `text` parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b8f86-139">İçin bir istek yapıldığında `/RouteParameter/amazing` , `<h1>` etiket içeriği olarak işlenir `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="b8f86-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b8f86-141">İsteğe bağlı parametreler desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-141">Optional parameters are supported.</span></span> <span data-ttu-id="b8f86-142">Aşağıdaki örnekte, `text` isteğe bağlı parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b8f86-143">Segment yoksa, değeri `Text` olarak ayarlanır `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b8f86-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b8f86-145">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b8f86-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="b8f86-146">Aşağıdaki örnekte, iki [ `@page` yönergeler](xref:mvc/views/razor#page) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="b8f86-147">İlk yönerge, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b8f86-148">İkinci yönerge, `{text}` yönlendirme parametresi değerini bileşenin `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="b8f86-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b8f86-150">[`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) Farklı bir isteğe bağlı parametre değeriyle aynı bileşene uygulama gezintisine izin vermek için yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8f86-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="b8f86-151">Önceki örneğe bağlı olarak, Kullanıcı ' dan veya ' den ' a kadar arasında `OnParametersSet` geziniyor olması gerekir `/RouteParameter` `/RouteParameter/amazing` `/RouteParameter/amazing` `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="b8f86-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="b8f86-152">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="b8f86-152">Route constraints</span></span>

<span data-ttu-id="b8f86-153">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b8f86-154">Aşağıdaki örnekte, `User` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="b8f86-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="b8f86-155">`Id`Istek URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="b8f86-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="b8f86-156">`Id`Segment bir Integer ( `int` ) türüdür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="b8f86-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="b8f86-158">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b8f86-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b8f86-159">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="b8f86-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b8f86-160">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="b8f86-160">Constraint</span></span> | <span data-ttu-id="b8f86-161">Örnek</span><span class="sxs-lookup"><span data-stu-id="b8f86-161">Example</span></span>           | <span data-ttu-id="b8f86-162">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="b8f86-162">Example Matches</span></span>                                                                  | <span data-ttu-id="b8f86-163">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="b8f86-163">Invariant</span></span><br><span data-ttu-id="b8f86-164">kültür</span><span class="sxs-lookup"><span data-stu-id="b8f86-164">culture</span></span><br><span data-ttu-id="b8f86-165">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="b8f86-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b8f86-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b8f86-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b8f86-167">Hayır</span><span class="sxs-lookup"><span data-stu-id="b8f86-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b8f86-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b8f86-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b8f86-169">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b8f86-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b8f86-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b8f86-171">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b8f86-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b8f86-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b8f86-173">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b8f86-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b8f86-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b8f86-175">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b8f86-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b8f86-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b8f86-177">Hayır</span><span class="sxs-lookup"><span data-stu-id="b8f86-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b8f86-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b8f86-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b8f86-179">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b8f86-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b8f86-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b8f86-181">Yes</span><span class="sxs-lookup"><span data-stu-id="b8f86-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b8f86-182">URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` <xref:System.DateTime> , her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="b8f86-183">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="b8f86-184">Noktalar içeren URL 'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="b8f86-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="b8f86-185">Barındırılan Blazor WebAssembly ve Blazor Server uygulamalar için, sunucu tarafı varsayılan yol şablonu, BIR istek URL 'sinin son segmentinin bir dosyanın istendiği nokta () içerdiğini varsayar `.` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="b8f86-186">Örneğin, URL, `https://localhost.com:5001/example/some.thing` yönlendirici tarafından adlı bir dosya için bir istek olarak yorumlanır `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="b8f86-187">Ek yapılandırma olmadan, bir uygulama bir  `some.thing` [ `@page` yönergeyle](xref:mvc/views/razor#page) bir bileşene yönlendirilecekse ve `some.thing` bir yol parametre değeri ise, uygulama 404-Found yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="b8f86-188">Bir nokta içeren bir veya daha fazla parametre içeren bir yol kullanmak için, uygulamanın rotayı özel bir şablonla yapılandırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="b8f86-189">`Example`URL 'nin son segmentinden bir yol parametresi alabilen aşağıdaki bileşeni göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="b8f86-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="b8f86-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b8f86-191">*`Server`* Barındırılan bir Blazor WebAssembly çözümün uygulamasının isteği yol parametresindeki bir noktayla yönlendirmesine izin vermek için `param` , içinde isteğe bağlı parametresine sahip bir geri dönüş dosya yolu şablonu ekleyin `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b8f86-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="b8f86-193">Bir uygulamayı, Blazor Server isteği Route parametresinde bir noktayla yönlendirmek üzere yapılandırmak için `param` , içinde isteğe bağlı parametresine sahip bir geri dönüş sayfası yol şablonu ekleyin `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b8f86-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="b8f86-195">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b8f86-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="b8f86-196">Catch-all Route parametreleri</span><span class="sxs-lookup"><span data-stu-id="b8f86-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b8f86-197">Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri, bileşenlerinde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="b8f86-198">Catch-all Route parametreleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b8f86-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="b8f86-199">Yol segmenti adıyla eşleşecek şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-199">Named to match the route segment name.</span></span> <span data-ttu-id="b8f86-200">Adlandırma, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="b8f86-201">Bir `string` tür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-201">A `string` type.</span></span> <span data-ttu-id="b8f86-202">Çerçeve otomatik atama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="b8f86-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="b8f86-203">URL 'nin sonunda.</span><span class="sxs-lookup"><span data-stu-id="b8f86-203">At the end of the URL.</span></span>

<span data-ttu-id="b8f86-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="b8f86-205">`/catch-all/this/is/a/test`Yol şablonuna sahıp URL için `/catch-all/{*pageRoute}` , değeri `PageRoute` olarak ayarlanır `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="b8f86-206">Yakalanan yolun eğik çizgileri ve kesimleri kodu çözülür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="b8f86-207">Bir yol şablonu için `/catch-all/{*pageRoute}` , URL 'nin `/catch-all/this/is/a%2Ftest%2A` verir `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b8f86-208">Catch-all Route parametreleri ASP.NET Core 5,0 veya üzeri sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="b8f86-209">Daha fazla bilgi için bu makalenin 5,0 sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="b8f86-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="b8f86-210">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="b8f86-210">URI and navigation state helpers</span></span>

<span data-ttu-id="b8f86-211"><xref:Microsoft.AspNetCore.Components.NavigationManager>C# kodunda URI 'leri ve gezintiyi yönetmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8f86-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="b8f86-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8f86-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="b8f86-213">Üye</span><span class="sxs-lookup"><span data-stu-id="b8f86-213">Member</span></span> | <span data-ttu-id="b8f86-214">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f86-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="b8f86-215">Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="b8f86-216">Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="b8f86-217">Genellikle, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> `href` `<base>` `wwwroot/index.html` ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` () içinde belge öğesindeki özniteliğine karşılık gelir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b8f86-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="b8f86-218">Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="b8f86-218">Navigates to the specified URI.</span></span> <span data-ttu-id="b8f86-219">`forceLoad`Şu ise `true` :</span><span class="sxs-lookup"><span data-stu-id="b8f86-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="b8f86-220">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="b8f86-221">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="b8f86-222">Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="b8f86-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="b8f86-223">Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="b8f86-224">Temel URI (örneğin, daha önce tarafından döndürülen bir URI) verildiğinde <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> , mutlak BIR URI 'yi taban URI önekine göre BIR URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="b8f86-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="b8f86-225">Olay için <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> , <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> gezinti olayları hakkında aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="b8f86-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="b8f86-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Yeni konumun URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="b8f86-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="b8f86-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Varsa `true` , Blazor Tarayıcıdan gezinme ele geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="b8f86-228">Varsa `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> gezintinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="b8f86-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="b8f86-229">Aşağıdaki bileşen:</span><span class="sxs-lookup"><span data-stu-id="b8f86-229">The following component:</span></span>

* <span data-ttu-id="b8f86-230">`Counter`Düğme seçildiğinde uygulamanın bileşenine () gider `Pages/Counter.razor` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="b8f86-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="b8f86-231">Abone olunarak konum değişti olayını işler <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b8f86-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b8f86-232">`HandleLocationChanged`Yöntemi, `Dispose` Framework tarafından çağrıldığında yok edilir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="b8f86-233">Yöntemi kaldırmak, bileşenin çöp toplamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="b8f86-234">Düğme seçildiğinde günlükçü uygulama aşağıdaki bilgileri günlüğe kaydeder:</span><span class="sxs-lookup"><span data-stu-id="b8f86-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="b8f86-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="b8f86-236">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="b8f86-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="b8f86-237">Sorgu dizesi ve ayrıştırma parametreleri</span><span class="sxs-lookup"><span data-stu-id="b8f86-237">Query string and parse parameters</span></span>

<span data-ttu-id="b8f86-238">Bir isteğin sorgu dizesi özelliğinden elde edilir <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b8f86-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="b8f86-239">Bir sorgu dizesinin parametrelerini ayrıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="b8f86-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="b8f86-240">Bir uygulama, <xref:Microsoft.AspNetCore.WebUtilities> API 'yi kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="b8f86-241">API uygulama için kullanılabilir değilse, [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)için uygulamanın proje dosyasına bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b8f86-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="b8f86-242">İle sorgu dizesini ayrıştırdıktan sonra değeri alın <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b8f86-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b8f86-243">Aşağıdaki `ParseQueryString` bileşen örneği adlı bir sorgu dizesi parametre anahtarını ayrıştırır `ship` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="b8f86-244">Örneğin, URL sorgu dizesi anahtar-değer çifti `?ship=Tardis` içindeki değeri yakalar `Tardis` `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="b8f86-245">Aşağıdaki örnekte, URL ile uygulamaya gidin `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="b8f86-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="b8f86-247">`NavLink` bileşeni</span><span class="sxs-lookup"><span data-stu-id="b8f86-247">`NavLink` component</span></span>

<span data-ttu-id="b8f86-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gezinti bağlantıları oluştururken, HTML köprü öğelerinin () yerine bir bileşen kullanın `<a>` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="b8f86-249">Bir <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `<a>` , `active` `href` geçerli URL ile eşleşip eşleşmediğini temel alarak bir CSS sınıfına geçiş yaptığı sürece bir öğesi gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b8f86-250">`active`Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="b8f86-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="b8f86-251">İsteğe bağlı olarak, <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> geçerli yol ile eşleştiğinde işlenen bağlantıya özel bır CSS sınıfı uygulamak için BIR CSS sınıfı adı atayın `href` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="b8f86-252">Aşağıdaki `NavMenu` Bileşen, [`Bootstrap`](https://getbootstrap.com/docs/) bileşenlerin nasıl kullanılacağını gösteren bir gezinti çubuğu oluşturur <xref:Microsoft.AspNetCore.Components.Routing.NavLink> :</span><span class="sxs-lookup"><span data-stu-id="b8f86-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b8f86-253">`NavMenu`Bileşen ( `NavMenu.razor` ), `Shared` proje şablonlarından oluşturulan bir uygulamanın klasöründe sağlanır Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8f86-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="b8f86-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki seçenek vardır `Match` `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="b8f86-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="b8f86-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> GEÇERLI URL 'nin tamamı eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="b8f86-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b8f86-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*varsayılan*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.</span><span class="sxs-lookup"><span data-stu-id="b8f86-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b8f86-257">Yukarıdaki örnekte, ana giriş URL 'siyle <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` eşleşir ve yalnızca `active` uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="b8f86-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="b8f86-258">İkincisi, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` Kullanıcı ön eki olan herhangi bir URL 'yi ziyaret ettiğinde sınıfı alır `component` (örneğin, `https://localhost:5001/component` ve `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="b8f86-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="b8f86-259">Ek <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="b8f86-260">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="b8f86-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="b8f86-261">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="b8f86-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="b8f86-262">BlazorAlt içeriği işleyen bir yol nedeniyle, `NavLink` bir döngü içindeki işleme bileşenleri, `for` `NavLink` (alt) bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa bir yerel dizin değişkeni gerektirir:</span><span class="sxs-lookup"><span data-stu-id="b8f86-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="b8f86-263">Bu senaryoda bir dizin değişkeni kullanılması yalnızca bileşeni değil, [alt içeriğinde](xref:blazor/components/index#child-content)bir döngü değişkeni kullanan **herhangi** bir alt bileşen için gereksinimdir `NavLink` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="b8f86-264">Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b8f86-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b8f86-265">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8f86-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b8f86-266">*Bu bölüm yalnızca uygulamalar için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b8f86-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="b8f86-267">Blazor Server[ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="b8f86-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b8f86-268">ASP.NET Core bir uygulama, içindeki etkileşimli bileşenlere yönelik gelen bağlantıları kabul edecek şekilde <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> yapılandırılmıştır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b8f86-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="b8f86-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b8f86-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="b8f86-270">Tipik yapılandırma tüm istekleri bir sayfaya yönlendirmesidir Razor ve bu, uygulamanın sunucu tarafı bölümü için ana bilgisayar görevi görür Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b8f86-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="b8f86-271">Kural gereği, *ana bilgisayar* sayfası genellikle `_Host.cshtml` `Pages` uygulamanın klasöründe adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="b8f86-272">Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="b8f86-273">Geri dönüş yolu, diğer yollar eşleşmediğinden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b8f86-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="b8f86-274">Bu, uygulamanın uygulamada bileşen yönlendirme ile kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b8f86-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="b8f86-275"><xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>Kök olmayan URL sunucusu barındırma için yapılandırma hakkında bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="b8f86-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
