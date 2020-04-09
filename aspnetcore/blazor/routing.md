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
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="dc75a-103">ASP.NET Çekirdek Blazor yönlendirme</span><span class="sxs-lookup"><span data-stu-id="dc75a-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="dc75a-104">Yazar: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dc75a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="dc75a-105">Blazor uygulamalarında gezinme bağlantıları `NavLink` oluşturmak için istekleri nasıl yönlendireceklerini ve bileşeni nasıl kullanacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="dc75a-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="dc75a-106">ASP.NET Core uç nokta yönlendirme tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="dc75a-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="dc75a-107">Blazor [Server, Core Endpoint YönlendirmeASP.NET](xref:fundamentals/routing)entegre edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="dc75a-108">Bir ASP.NET Core uygulaması, etkileşimli bileşenler için gelen `MapBlazorHub` `Startup.Configure`bağlantıları kabul etmek üzere yapılandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="dc75a-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="dc75a-109">En tipik yapılandırma, tüm istekleri Blazor Server uygulamasının sunucu tarafındaki bölümünün ana bilgisayar ı olarak gören bir Razor sayfasına yönlendirmektir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="dc75a-110">Kural olarak, *ana sayfa* genellikle *_Host.cshtml*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="dc75a-111">Ana bilgisayar dosyasında belirtilen rota, rota eşleştirmede düşük bir önceliğe sahip çalıştığından *geri dönüş rotası* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="dc75a-112">Diğer rotalar eşleşmediğinde geri dönüş rotası dikkate alınr.</span><span class="sxs-lookup"><span data-stu-id="dc75a-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="dc75a-113">Bu, uygulamanın Blazor Server uygulamasına müdahale etmeden diğer denetleyicileri ve sayfaları kullanmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="dc75a-114">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="dc75a-114">Route templates</span></span>

<span data-ttu-id="dc75a-115">Bileşen, `Router` belirtilen bir rota ile her bileşene yönlendirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc75a-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="dc75a-116">Bileşen `Router` *App.razor* dosyasında görünür:</span><span class="sxs-lookup"><span data-stu-id="dc75a-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="dc75a-117">Yönergesi olan `@page` bir *.razor* dosyası derlendiğinde, <xref:Microsoft.AspNetCore.Components.RouteAttribute> oluşturulan sınıfa rota şablonu belirten bir bilgi verilir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="dc75a-118">Çalışma zamanında `RouteView` bileşen:</span><span class="sxs-lookup"><span data-stu-id="dc75a-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="dc75a-119">`RouteData` İstenilen `Router` parametrelerle birlikte alır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="dc75a-120">Belirtilen bileşeni, belirtilen parametreleri kullanarak düzeniyle (veya isteğe bağlı varsayılan düzenle) işler.</span><span class="sxs-lookup"><span data-stu-id="dc75a-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="dc75a-121">İsteğe bağlı olarak, düzen belirtmeyen bileşenler için kullanılacak düzen sınıfı olan bir `DefaultLayout` parametre belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc75a-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="dc75a-122">Varsayılan Blazor şablonları `MainLayout` bileşeni belirtir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="dc75a-123">*MainLayout.razor* şablon projenin *Paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="dc75a-124">Düzenler hakkında daha fazla <xref:blazor/layouts>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="dc75a-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="dc75a-125">Bir bileşene birden çok rota şablonu uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="dc75a-126">Aşağıdaki bileşen isteklere `/BlazorRoute` yanıt `/DifferentBlazorRoute`verir ve:</span><span class="sxs-lookup"><span data-stu-id="dc75a-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="dc75a-127">URL'lerin doğru şekilde çözümlemesi için `<base>` uygulamanın *wwwroot/index.html* dosyasına (Blazor WebAssembly) veya *Pages/_Host.cshtml* dosyasına (Blazor `href` Server)`<base href="/">`öznitelikte belirtilen uygulama temel yolu içeren bir etiket eklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="dc75a-128">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="dc75a-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="dc75a-129">İçerik bulunamadığında özel içerik sağlayın</span><span class="sxs-lookup"><span data-stu-id="dc75a-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="dc75a-130">Bileşen, `Router` istenen rota için içerik bulunamıyorsa uygulamanın özel içerik belirtmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="dc75a-131">*App.razor* dosyasında, `NotFound` `Router` bileşenin şablon parametresinde özel içerik ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="dc75a-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="dc75a-132">Etiketlerin `<NotFound>` içeriği, diğer etkileşimli bileşenler gibi rasgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="dc75a-133">İçerime varsayılan düzen `NotFound` uygulamak <xref:blazor/layouts>için bkz.</span><span class="sxs-lookup"><span data-stu-id="dc75a-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="dc75a-134">Birden çok derlemeden bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="dc75a-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="dc75a-135">Bileşenin, `AdditionalAssemblies` routable bileşenlerini ararken göz önünde bulundurması gereken `Router` ek derlemeler belirtmek için parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="dc75a-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="dc75a-136">Belirtilen `AppAssembly`derlemeler, belirtilen derlemeye ek olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="dc75a-137">Aşağıdaki örnekte, `Component1` başvurulan sınıf kitaplığında tanımlanan bir routable bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="dc75a-138">Aşağıdaki `AdditionalAssemblies` örnek, aşağıdaki yönlendirme desteğiile `Component1`sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="dc75a-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="dc75a-139">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="dc75a-139">Route parameters</span></span>

<span data-ttu-id="dc75a-140">Yönlendirici, ilgili bileşen parametrelerini aynı ada (büyük/küçük harf duyarsız) doldurmak için rota parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="dc75a-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="dc75a-141">İsteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="dc75a-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="dc75a-142">Önceki `@page` örnekte iki yönerge uygulanır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="dc75a-143">İlk bir parametre olmadan bileşene navigasyon izin verir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="dc75a-144">İkinci `@page` yönerge `{text}` rota parametresini alır ve `Text` değeri özelliğe atar.</span><span class="sxs-lookup"><span data-stu-id="dc75a-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="dc75a-145">Rota kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="dc75a-145">Route constraints</span></span>

<span data-ttu-id="dc75a-146">Bir rota kısıtlaması, bir bileşene bir rota kesiminde tür eşleştirmesi zorlar.</span><span class="sxs-lookup"><span data-stu-id="dc75a-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="dc75a-147">Aşağıdaki örnekte, `Users` bileşene giden yol yalnızca aşağıdaki durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="dc75a-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="dc75a-148">İstek `Id` URL'sinde bir rota kesimi bulunur.</span><span class="sxs-lookup"><span data-stu-id="dc75a-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="dc75a-149">Segment `Id` bir karşıcıdır`int`( ).</span><span class="sxs-lookup"><span data-stu-id="dc75a-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="dc75a-150">Aşağıdaki tabloda gösterilen rota kısıtlamaları kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="dc75a-151">Değişmez kültürle eşleşen rota kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="dc75a-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="dc75a-152">Kısıtlama</span><span class="sxs-lookup"><span data-stu-id="dc75a-152">Constraint</span></span> | <span data-ttu-id="dc75a-153">Örnek</span><span class="sxs-lookup"><span data-stu-id="dc75a-153">Example</span></span>           | <span data-ttu-id="dc75a-154">Örnek Eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="dc75a-154">Example Matches</span></span>                                                                  | <span data-ttu-id="dc75a-155">Sabit</span><span class="sxs-lookup"><span data-stu-id="dc75a-155">Invariant</span></span><br><span data-ttu-id="dc75a-156">kültür</span><span class="sxs-lookup"><span data-stu-id="dc75a-156">culture</span></span><br><span data-ttu-id="dc75a-157">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="dc75a-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="dc75a-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="dc75a-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="dc75a-159">Hayır</span><span class="sxs-lookup"><span data-stu-id="dc75a-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="dc75a-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="dc75a-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="dc75a-161">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="dc75a-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="dc75a-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="dc75a-163">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="dc75a-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="dc75a-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="dc75a-165">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="dc75a-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="dc75a-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="dc75a-167">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="dc75a-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="dc75a-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="dc75a-169">Hayır</span><span class="sxs-lookup"><span data-stu-id="dc75a-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="dc75a-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="dc75a-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="dc75a-171">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="dc75a-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="dc75a-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="dc75a-173">Evet</span><span class="sxs-lookup"><span data-stu-id="dc75a-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="dc75a-174">URL'yi doğrulayan ve CLR türüne dönüştürülen `int` (örneğin `DateTime`veya) her zaman değişmez kültürü kullanan yol kısıtlamaları.</span><span class="sxs-lookup"><span data-stu-id="dc75a-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="dc75a-175">Bu kısıtlamalar, URL'nin yerelleştirilebilir olmadığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="dc75a-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="dc75a-176">Nokta içeren URL'lerle yönlendirme</span><span class="sxs-lookup"><span data-stu-id="dc75a-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="dc75a-177">Blazor Server uygulamalarında, *_Host.cshtml'deki* varsayılan rota `/` ( ).`@page "/"`</span><span class="sxs-lookup"><span data-stu-id="dc75a-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="dc75a-178">Nokta (`.`) içeren bir istek URL'si, URL bir dosya istemek için göründüğünden varsayılan rotayla eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="dc75a-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="dc75a-179">Bir Blazor uygulaması, var olmayan statik bir dosya için *404 - Bulunamadı* yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="dc75a-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="dc75a-180">Nokta içeren yolları kullanmak için *_Host.cshtml'i* aşağıdaki rota şablonuyla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="dc75a-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="dc75a-181">Şablon `"/{**path}"` şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="dc75a-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="dc75a-182">Çift yıldız işareti *catch-all* sözdizimi (`**`) ileri kesikleri kodlamadan birden`/`çok klasör sınırları boyunca yolu yakalamak için ( ).</span><span class="sxs-lookup"><span data-stu-id="dc75a-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="dc75a-183">`path`rota parametre adı.</span><span class="sxs-lookup"><span data-stu-id="dc75a-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="dc75a-184">*Catch-all* parametre sözdizimi (`*`/`**`) Razor bileşenlerinde **desteklenmez** (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="dc75a-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="dc75a-185">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="dc75a-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="dc75a-186">NavLink bileşeni</span><span class="sxs-lookup"><span data-stu-id="dc75a-186">NavLink component</span></span>

<span data-ttu-id="dc75a-187">Navigasyon `NavLink` bağlantıları oluştururken HTML köprü`<a>`öğeleri () yerine bir bileşen kullanın.</span><span class="sxs-lookup"><span data-stu-id="dc75a-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="dc75a-188">Bileşen, `NavLink` geçerli URL `<a>` ile `href` eşleşip eşleşmediğine `active` bağlı olarak bir CSS sınıfını geçişlemesi dışında bir öğe gibi olur.</span><span class="sxs-lookup"><span data-stu-id="dc75a-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="dc75a-189">Sınıf, `active` kullanıcının görüntülenen gezinme bağlantıları arasında hangi sayfanın etkin sayfa olduğunu anlamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="dc75a-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="dc75a-190">Aşağıdaki `NavMenu` bileşen, bileşenlerin nasıl kullanılacağını `NavLink` gösteren bir [Bootstrap](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="dc75a-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="dc75a-191">Öğenin `NavLinkMatch` `Match` özniteliğine atayabileceğiniz iki seçenek vardır: `<NavLink>`</span><span class="sxs-lookup"><span data-stu-id="dc75a-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="dc75a-192">`NavLinkMatch.All`&ndash; Geçerli `NavLink` URL'nin tamamıyla eşleştiğinde etkindir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="dc75a-193">`NavLinkMatch.Prefix`(*varsayılan*) &ndash; Geçerli `NavLink` URL'nin herhangi bir önekiyle eşleştiğinde etkindir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="dc75a-194">Önceki örnekte, Ev `NavLink` `href=""` ev URL'si eşleşir ve yalnızca uygulamanın `active` varsayılan temel yol URL'sinde (örneğin) `https://localhost:5001/`CSS sınıfını alır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="dc75a-195">İkinci `NavLink` kullanıcı `active` önek ile herhangi bir `MyComponent` URL ziyaret ettiğinde `https://localhost:5001/MyComponent` sınıf `https://localhost:5001/MyComponent/AnotherSegment`alır (örneğin, ve).</span><span class="sxs-lookup"><span data-stu-id="dc75a-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="dc75a-196">Ek `NavLink` bileşen öznitelikleri işlenen bağlantı etiketine aktarılır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="dc75a-197">Aşağıdaki örnekte, `NavLink` bileşen özniteliği içerir: `target`</span><span class="sxs-lookup"><span data-stu-id="dc75a-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="dc75a-198">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="dc75a-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="dc75a-199">URI ve navigasyon durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="dc75a-199">URI and navigation state helpers</span></span>

<span data-ttu-id="dc75a-200">URI'lerle çalışmak ve C# kodunda gezinmek için kullanın. <xref:Microsoft.AspNetCore.Components.NavigationManager></span><span class="sxs-lookup"><span data-stu-id="dc75a-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="dc75a-201">`NavigationManager`aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc75a-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="dc75a-202">Üye</span><span class="sxs-lookup"><span data-stu-id="dc75a-202">Member</span></span> | <span data-ttu-id="dc75a-203">Açıklama</span><span class="sxs-lookup"><span data-stu-id="dc75a-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="dc75a-204">Urı</span><span class="sxs-lookup"><span data-stu-id="dc75a-204">Uri</span></span> | <span data-ttu-id="dc75a-205">Geçerli mutlak URI alır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="dc75a-206">Baseuri</span><span class="sxs-lookup"><span data-stu-id="dc75a-206">BaseUri</span></span> | <span data-ttu-id="dc75a-207">Mutlak bir URI üretmek için göreceli URI yollarına hazırlanabilen temel URI'yi (sondaki bir eğik çizgiyle) alır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="dc75a-208">`BaseUri` Genellikle, `href` *belgenin* Blazor `<base>` wwwroot/index.html (WebAssembly) veya *Pages/_Host.cshtml* (Server)Blazor öğesindeki özniteliğe karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="dc75a-209">Gezinme</span><span class="sxs-lookup"><span data-stu-id="dc75a-209">NavigateTo</span></span> | <span data-ttu-id="dc75a-210">Belirtilen URI'ye doğru iletin.</span><span class="sxs-lookup"><span data-stu-id="dc75a-210">Navigates to the specified URI.</span></span> <span data-ttu-id="dc75a-211">`true`Ise: `forceLoad`</span><span class="sxs-lookup"><span data-stu-id="dc75a-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="dc75a-212">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="dc75a-213">Tarayıcı, URI normalde istemci tarafı yönlendirici tarafından işlenir olsun veya olmasın, sunucudan yeni sayfa yüklemek zorunda kalır.</span><span class="sxs-lookup"><span data-stu-id="dc75a-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="dc75a-214">Konum Değiştirildi</span><span class="sxs-lookup"><span data-stu-id="dc75a-214">LocationChanged</span></span> | <span data-ttu-id="dc75a-215">Gezinti yeri değiştiğinde ateşleyen bir olay.</span><span class="sxs-lookup"><span data-stu-id="dc75a-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="dc75a-216">Toabsoluteuri</span><span class="sxs-lookup"><span data-stu-id="dc75a-216">ToAbsoluteUri</span></span> | <span data-ttu-id="dc75a-217">Göreceli bir URI'yi mutlak bir URI'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc75a-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="dc75a-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="dc75a-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="dc75a-219">Bir temel URI (örneğin, bir URI `GetBaseUri`daha önce döndürülen) göz önüne alındığında, temel URI öneki göre bir URI göreli mutlak bir URI dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc75a-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="dc75a-220">Düğme seçildiğinde aşağıdaki bileşen uygulamanın `Counter` bileşenine yönlendirilir:</span><span class="sxs-lookup"><span data-stu-id="dc75a-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="dc75a-221">Aşağıdaki bileşen, değiştirilen bir konumu işler.</span><span class="sxs-lookup"><span data-stu-id="dc75a-221">The following component handles a location changed event.</span></span> <span data-ttu-id="dc75a-222">Yöntem, `HandleLocationChanged` çerçeve tarafından `Dispose` çağrıldığında çözülz.</span><span class="sxs-lookup"><span data-stu-id="dc75a-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="dc75a-223">Yöntemin çözülmesi bileşenin çöp toplaması için izin verir.</span><span class="sxs-lookup"><span data-stu-id="dc75a-223">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="dc75a-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>olay hakkında aşağıdaki bilgileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="dc75a-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="dc75a-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Yeni konumun URL'si.</span><span class="sxs-lookup"><span data-stu-id="dc75a-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="dc75a-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Eğer, Blazor tarayıcıdan navigasyon ele geçirdi.</span><span class="sxs-lookup"><span data-stu-id="dc75a-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="dc75a-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) gezintisinin oluşmasına neden oldu.</span><span class="sxs-lookup"><span data-stu-id="dc75a-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="dc75a-228">Bileşen imhası hakkında daha <xref:blazor/lifecycle#component-disposal-with-idisposable>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="dc75a-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
