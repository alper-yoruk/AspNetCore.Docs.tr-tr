---
<span data-ttu-id="b6bb4-101">Başlık: ' ASP.NET Core bileşenleri oluşturma ve kullanma Razor ' Yazar: Açıklama: ' Razor verileri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="b6bb4-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="b6bb4-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6bb4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6bb4-103">'Blazor'</span></span>
- <span data-ttu-id="b6bb4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6bb4-104">'Identity'</span></span>
- <span data-ttu-id="b6bb4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6bb4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6bb4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6bb4-106">'Razor'</span></span>
- <span data-ttu-id="b6bb4-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="b6bb4-108">ASP.NET Core bileşenleri oluşturma ve kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="b6bb4-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="b6bb4-109">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="b6bb4-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="b6bb4-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6bb4-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="b6bb4-111">uygulamalar, *Bileşenler*kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-111"> apps are built using *components*.</span></span> <span data-ttu-id="b6bb4-112">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="b6bb4-113">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="b6bb4-114">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="b6bb4-115">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="b6bb4-116">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="b6bb4-116">Component classes</span></span>

<span data-ttu-id="b6bb4-117">Bileşenler, [Razor](xref:mvc/views/razor) C# ve HTML biçimlendirme birleşimi kullanılarak bileşen dosyalarında (*. Razor*) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="b6bb4-118">İçindeki bir bileşen Blazor bir \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="b6bb4-119">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="b6bb4-120">Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="b6bb4-121">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="b6bb4-122">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir *Razor* .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="b6bb4-123">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="b6bb4-124">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="b6bb4-125">Bileşen sınıfının üyeleri bir [`@code`][1] blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="b6bb4-126">[`@code`][1]Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="b6bb4-127">Birden çok [`@code`][1] blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="b6bb4-128">Bileşen üyeleri, ile başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir `@` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="b6bb4-129">Örneğin, bir C# alanı alan adının önüne eklenerek işlenir `@` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="b6bb4-130">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="b6bb4-131">`headingFontStyle`için CSS özellik değerine `font-style` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="b6bb4-132">`headingText``<h1>`öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="b6bb4-133">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="b6bb4-134">ardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-134"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="b6bb4-135">Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="b6bb4-136">Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="b6bb4-137">Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="b6bb4-138">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="b6bb4-139">Uygulamanın kök ad alanı ise `BlazorApp` ve `Counter` bileşen *Sayfalar* klasöründe bulunuyorsa:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="b6bb4-140">`Counter`Bileşenin ad alanı `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="b6bb4-141">Bileşenin tam nitelikli tür adı `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="b6bb4-142">Bileşenleri tutan özel klasörler için, `using` üst bileşene veya uygulamanın *_Imports. Razor* dosyasına bir ifade ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="b6bb4-143">Aşağıdaki örnek *Bileşenler* klasöründeki bileşenleri kullanılabilir hale getirir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="b6bb4-144">Alternatif olarak, bir bileşene doğrudan başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="b6bb4-145">Daha fazla bilgi için [bileşenleri Içeri aktarma](#import-components) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="b6bb4-146">sözdizimi</span><span class="sxs-lookup"><span data-stu-id="b6bb4-146"> syntax</span></span>

Razor<span data-ttu-id="b6bb4-147">uygulamalardaki bileşenler Blazor yaygın olarak Razor söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="b6bb4-148">RazorBiçimlendirme diline alışkın değilseniz, devam etmeden önce okumanız önerilir <xref:mvc/views/razor> .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="b6bb4-149">Söz dizimi üzerindeki içeriğe erişirken Razor , aşağıdaki bölümlere özel bir dikkat ödeyin:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="b6bb4-150">[Yönergeler](xref:mvc/views/razor#directives) &ndash; `@`-ön ek ayrılmış anahtar sözcükler genellikle bileşen biçimlendirmesinin ayrıştırılma veya işlev şeklini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-150">[Directives](xref:mvc/views/razor#directives) &ndash; `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="b6bb4-151">[Yönerge öznitelikleri](xref:mvc/views/razor#directive-attributes) &ndash; `@`-ön ek ayrılmış anahtar sözcükler genellikle bileşen öğelerinin ayrıştırılıp veya işlevin şeklini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-151">[Directive attributes](xref:mvc/views/razor#directive-attributes) &ndash; `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="b6bb4-152">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="b6bb4-152">Static assets</span></span>

Blazor<span data-ttu-id="b6bb4-153">Projenin [Web kökü (Wwwroot) klasörü](xref:fundamentals/index#web-root)altında statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="b6bb4-154">`/`Statik bir varlık için Web köküne başvurmak üzere temel göreli bir yol () kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="b6bb4-155">Aşağıdaki örnekte, *logo. png* fiziksel olarak *{Project root}/Wwwroot/Images* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="b6bb4-156">bileşenler, **not** tilde işareti gösterimini ( `~/` ) desteklemez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="b6bb4-157">Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:host-and-deploy/blazor/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="b6bb4-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="b6bb4-158">Etiket Yardımcıları bileşenlerde desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="b6bb4-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="b6bb4-159">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , Razor bileşenlerinde (*. Razor* dosyaları) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="b6bb4-160">' De etiket Yardımcısı benzeri işlevsellik sağlamak için Blazor , etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="b6bb4-161">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="b6bb4-161">Use components</span></span>

<span data-ttu-id="b6bb4-162">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="b6bb4-163">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="b6bb4-164">*Index. Razor* dosyasında aşağıdaki biçimlendirme bir örneği işler `HeadingComponent` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="b6bb4-165">*Bileşenler/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="b6bb4-166">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="b6bb4-167">[`@using`][2]Bileşenin ad alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="b6bb4-168">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="b6bb4-168">Routing</span></span>

<span data-ttu-id="b6bb4-169">Uygulamasında yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="b6bb4-170">Razor [`@page`][9] Yönergeyle bir dosya derlendiğinde, oluşturulan sınıfa <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> yol şablonunu belirten bir değer verilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="b6bb4-171">Çalışma zamanında, yönlendirici bileşen sınıflarını bir ile arar <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ve hangi bileşenin Istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-171">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="b6bb4-172">Daha fazla bilgi için bkz. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="b6bb4-173">Parametreler</span><span class="sxs-lookup"><span data-stu-id="b6bb4-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="b6bb4-174">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="b6bb4-174">Route parameters</span></span>

<span data-ttu-id="b6bb4-175">Bileşenler, yönergede belirtilen yol şablonundan rota parametreleri alabilir [`@page`][9] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="b6bb4-176">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="b6bb4-177">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="b6bb4-178">İsteğe bağlı parametreler desteklenmez, bu nedenle [`@page`][9] Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="b6bb4-179">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b6bb4-180">İkinci [`@page`][9] yönerge, `{text}` route parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="b6bb4-181">*Catch-all* `*` / `**` Birden çok klasör sınırlarındaki yolu yakalayan catch-all parametresi sözdizimi () **,** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="b6bb4-182">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="b6bb4-182">Component parameters</span></span>

<span data-ttu-id="b6bb4-183">Bileşenler, bileşen *component parameters*sınıfında [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ] (XREF: Microsoft. Aspnetcore. components. ParameterAttribute) özniteliğiyle ortak özellikler kullanılarak tanımlanan bileşen parametrelerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-183">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="b6bb4-184">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="b6bb4-185">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="b6bb4-186">Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` öğesinin özelliğinin değerini ayarlar `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="b6bb4-187">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="b6bb4-188">Kendi *bileşen parametrelerine*yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="b6bb4-189">Daha fazla bilgi için [kendi parametre özelliklerine yazan bileşenleri oluşturma](#dont-create-components-that-write-to-their-own-parameter-properties) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="b6bb4-190">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="b6bb4-190">Child content</span></span>

<span data-ttu-id="b6bb4-191">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-191">Components can set the content of another component.</span></span> <span data-ttu-id="b6bb4-192">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="b6bb4-193">Aşağıdaki örnekte, öğesinin `ChildComponent` `ChildContent` <xref:Microsoft.AspNetCore.Components.RenderFragment> işlemek için bir kullanıcı arabirimi segmentini temsil eden öğesini temsil eden bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="b6bb4-194">Değeri, `ChildContent` bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="b6bb4-195">Değeri, `ChildContent` ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="b6bb4-196">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="b6bb4-197">İçeriği alan özelliğin <xref:Microsoft.AspNetCore.Components.RenderFragment> `ChildContent` kural tarafından adlandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-197">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="b6bb4-198">`ParentComponent`Örnek uygulamadaki ' de `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz `<ChildComponent>` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="b6bb4-199">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="b6bb4-200">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="b6bb4-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="b6bb4-201">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="b6bb4-202">Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* [`@attributes`][3] Razor .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="b6bb4-203">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="b6bb4-204">Örneğin, `<input>` çok sayıda parametreyi destekleyen bir için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="b6bb4-205">Aşağıdaki örnekte, ilk `<input>` öğesi ( `id="useIndividualParams"` ) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe ( `id="useAttributesDict"` ) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="b6bb4-206">Parametrenin türü `IEnumerable<KeyValuePair<string, object>>` dize anahtarlarıyla gerçekleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="b6bb4-207">`IReadOnlyDictionary<string, object>`Bu senaryoda ayrıca bir seçenek de vardır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="b6bb4-208">`<input>`Her iki yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-208">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="b6bb4-209">Rastgele öznitelikleri kabul etmek için [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) özelliği olarak ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-209">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="b6bb4-210"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Üzerindeki özelliği, [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-210">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="b6bb4-211">Bir bileşen yalnızca ile tek bir parametre tanımlayabilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-211">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="b6bb4-212">İle kullanılan özellik türü <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `Dictionary<string, object>` dize anahtarlarıyla atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-212">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="b6bb4-213">`IEnumerable<KeyValuePair<string, object>>``IReadOnlyDictionary<string, object>`Ayrıca, Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="b6bb4-214">[`@attributes`][3]Öğe özniteliklerinin konumuna göreli konumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="b6bb4-215">Öğe üzerinde ne zaman bırakıldığında [`@attributes`][3] , öznitelikler sağdan sola (son-ilk) işlenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="b6bb4-216">Bir bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun `Child` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="b6bb4-217">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b6bb4-218">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b6bb4-219">`Child`Bileşenin `extra` özniteliği öğesinin sağına ayarlanır [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="b6bb4-220">`Parent` `<div>` `extra="5"` Öznitelikler sağdan sola (en son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="b6bb4-221">Aşağıdaki örnekte, `extra` ve sırası [`@attributes`][3] `Child` bileşen tarafından tersine çevrilir `<div>` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="b6bb4-222">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b6bb4-223">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b6bb4-224">Bileşendeki işlenen `<div>` `Parent` `extra="10"` ek öznitelik ile geçirildiğinde şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="b6bb4-225">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="b6bb4-225">Capture references to components</span></span>

<span data-ttu-id="b6bb4-226">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya gibi komutları bu örneğe verebilirsiniz `Show` `Reset` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="b6bb4-227">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-227">To capture a component reference:</span></span>

* <span data-ttu-id="b6bb4-228">[`@ref`][4]Alt bileşene bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="b6bb4-229">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-229">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="b6bb4-230">Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="b6bb4-231">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6bb4-232">`loginDialog`Değişken yalnızca bileşen işlendikten sonra ve çıktısı öğesi içerdiğinde doldurulur `MyLoginDialog` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="b6bb4-233">Bu noktaya kadar başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="b6bb4-234">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için [Onafterrenderasync veya OnAfterRender yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="b6bb4-235">Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="b6bb4-236">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="b6bb4-237">Bileşen başvuruları JavaScript koduna geçirilmiyor.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="b6bb4-238">Bileşen başvuruları yalnızca .NET kodunda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="b6bb4-239">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="b6bb4-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="b6bb4-240">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="b6bb4-241">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="b6bb4-242">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="b6bb4-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="b6bb4-243"><xref:System.Threading.SynchronizationContext>yürütmenin tek bir mantıksal iş parçacığını zorlamak için bir eşitleme bağlamı () kullanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-243"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="b6bb4-244">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/lifecycle) ve tarafından oluşturulan tüm olay geri çağırmaları Blazor eşitleme bağlamında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="b6bb4-245">Sunucunun eşitleme bağlamı, tek iş parçacıklı bir ortamı öykünmeye çalışır ve bu sayede tek iş parçacıklı olan tarayıcıdaki WebAssembly modeliyle yakından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-245"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="b6bb4-246">Belirli bir zamanda, iş, tek bir mantıksal iş parçacığının izlenimi vererek tam olarak bir iş parçacığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="b6bb4-247">Aynı anda iki işlem yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="b6bb4-248">Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi bir dış olay temel alınarak güncellenmesi gerekir, bu `InvokeAsync` yöntemi, Blazor eşitleme bağlamına dağımakta olan yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="b6bb4-249">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="b6bb4-250">`NotifierService`Bir tekın olarak kaydolun:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="b6bb4-251">BlazorWebassembly ' de hizmeti hizmetine kaydedin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="b6bb4-252">BlazorSunucusunda, hizmetini hizmetine kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="b6bb4-253">`NotifierService`Bir bileşeni güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-253">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="b6bb4-254">Önceki örnekte, `NotifierService` bileşen `OnNotify` metodunu Blazor eşitleme bağlamı dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="b6bb4-255">`InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="b6bb4-256">\@Öğe ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="b6bb4-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="b6bb4-257">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor Bu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="b6bb4-258">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="b6bb4-259">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-259">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b6bb4-260">Koleksiyonun içeriği, `People` ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="b6bb4-261">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı parametre değerleri almak için değişebilir `Details` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="b6bb4-262">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="b6bb4-263">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="b6bb4-264">Eşleme işlemi, [`@key`][5] Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="b6bb4-265">[`@key`][5], anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b6bb4-266">`People`Koleksiyon değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekler ve örnekler arasındaki ilişkilendirmeyi korur `person` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="b6bb4-267">Bir, `Person` `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="b6bb4-268">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b6bb4-269">Listedeki bir `Person` konuma eklenirse, `<DetailsEditor>` ilgili konuma bir yeni örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="b6bb4-270">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b6bb4-271">`Person`Girişler yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="b6bb4-272">Bazı senaryolarda, kullanımı [`@key`][5] rerendering karmaşıklığını en aza indirir ve odak konumu gıbı Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6bb4-273">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="b6bb4-274">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="b6bb4-275">Anahtar ne zaman kullanılır? \@</span><span class="sxs-lookup"><span data-stu-id="b6bb4-275">When to use \@key</span></span>

<span data-ttu-id="b6bb4-276">Genellikle, [`@key`][5] her bir liste işlendiğinde (örneğin, bir [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) bloğunda) ve tanımlamak için uygun bir değer varsa, bu durum kullanılır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="b6bb4-277">Bir [`@key`][5] Blazor nesne değiştiğinde bir öğeyi veya bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="b6bb4-278">`@currentPerson`Değişiklik olursa, [`@key`][5] öznitelik yönergesi Blazor tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="b6bb4-279">Değişiklik sırasında hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="b6bb4-280">Anahtar ne zaman kullanılmaz? \@</span><span class="sxs-lookup"><span data-stu-id="b6bb4-280">When not to use \@key</span></span>

<span data-ttu-id="b6bb4-281">İle yayılma yaparken bir performans maliyeti vardır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="b6bb4-282">Performans maliyeti büyük değildir, ancak yalnızca [`@key`][5] öğenin veya bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="b6bb4-283">[`@key`][5]Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="b6bb4-284">Kullanmanın tek avantajı [`@key`][5] model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="b6bb4-285">Anahtar için kullanılacak değerler \@</span><span class="sxs-lookup"><span data-stu-id="b6bb4-285">What values to use for \@key</span></span>

<span data-ttu-id="b6bb4-286">Genellikle, için aşağıdaki değer türlerinden birini sağlamak mantıklı olur [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="b6bb4-287">Model nesne örnekleri (örneğin, `Person` Önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="b6bb4-288">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="b6bb4-289">Benzersiz tanımlayıcılar (örneğin, veya türündeki birincil anahtar değerleri `int` `string` `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="b6bb4-290">Bu değerlerin çakışmayın için kullanıldığından emin olun [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="b6bb4-291">Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="b6bb4-292">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="b6bb4-293">Kendi parametre özelliklerine yazan bileşenler oluşturmayın</span><span class="sxs-lookup"><span data-stu-id="b6bb4-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="b6bb4-294">Parametreleri aşağıdaki koşullarda üzerine yazılır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="b6bb4-295">Bir alt bileşenin içeriği ile işlenir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-295">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="b6bb4-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="b6bb4-297">Üst bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> çağrıldığında ve alt bileşene yeni parametre değerleri sağlandığında parametreler sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="b6bb4-298">Aşağıdaki bileşeni göz önünde bulundurun `Expander` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="b6bb4-299">Alt içeriği işler.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-299">Renders child content.</span></span>
* <span data-ttu-id="b6bb4-300">Bileşen parametresiyle alt içeriğin gösterilmesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-300">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="b6bb4-301">`Expander`Bileşen, çağıraetkileyebilecek bir üst bileşene eklenir <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-301">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="b6bb4-302">Başlangıçta, `Expander` bileşenleri özellikleri bir kez değiştiğinde bağımsız olarak davranır `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="b6bb4-303">Alt bileşenler, durumlarını beklendiği gibi korur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="b6bb4-304"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Üst öğede çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değeri () olarak döndürülür `true` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-304">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="b6bb4-305">İkinci `Expander` `Expanded` bileşende hiçbir alt içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="b6bb4-306">Önceki senaryodaki durumu korumak için bileşen içindeki *özel bir alanı* kullanarak, onun geçiş `Expander` durumunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="b6bb4-307">Aşağıdaki `Expander` bileşen:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-307">The following `Expander` component:</span></span>

* <span data-ttu-id="b6bb4-308">Üst öğeden `Expanded` bileşen parametre değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="b6bb4-309">*private field* `expanded` [OnInitialized olaydaki](xref:blazor/lifecycle#component-initialization-methods)bir özel alana () bileşen parametre değerini atar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="b6bb4-310">İç geçiş durumunu korumak için özel alanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-310">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="b6bb4-311">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="b6bb4-311">Partial class support</span></span>

Razor<span data-ttu-id="b6bb4-312">bileşenler kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="b6bb4-313">bileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="b6bb4-314">C# kodu, [`@code`][1] tek bir dosyada HTML işaretlemesi ve kodu olan bir blokta tanımlanmıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="b6bb4-315">Şablonlar, Razor Bu yaklaşımı kullanarak bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="b6bb4-316">C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="b6bb4-317">Aşağıdaki örnek, `Counter` [`@code`][1] bir şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir Blazor .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="b6bb4-318">HTML Markup, Razor Code ve C# kodu aynı dosyada:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="b6bb4-319">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-319">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="b6bb4-320">`Counter`Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="b6bb4-321">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="b6bb4-322">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-322">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="b6bb4-323">Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="b6bb4-324">Bileşenler tarafından kullanılan tipik ad alanları Razor şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="b6bb4-325">Temel sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="b6bb4-325">Specify a base class</span></span>

<span data-ttu-id="b6bb4-326">[`@inherits`][6]Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="b6bb4-327">Aşağıdaki örnek, bileşenin `BlazorRocksBase` özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel sınıfı nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="b6bb4-328">Taban sınıfın türevi olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-328">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="b6bb4-329">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="b6bb4-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-330">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="b6bb4-331">Bir öznitelik belirtin</span><span class="sxs-lookup"><span data-stu-id="b6bb4-331">Specify an attribute</span></span>

<span data-ttu-id="b6bb4-332">Öznitelikler Razor , yönergeyle birlikte bileşenlerde belirtilebilir [`@attribute`][7] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="b6bb4-333">Aşağıdaki örnek, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşen sınıfına özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-333">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="b6bb4-334">Bileşenleri içeri aktar</span><span class="sxs-lookup"><span data-stu-id="b6bb4-334">Import components</span></span>

<span data-ttu-id="b6bb4-335">İle yazılmış bir bileşenin ad alanı, Razor tabanlıdır (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="b6bb4-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="b6bb4-336">[`@namespace`][8]Razordosya (*. Razor*) biçimlendirmesinde atama ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="b6bb4-337">Proje, `RootNamespace` Proje dosyasında ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="b6bb4-338">Proje dosyasının dosya adından (*. csproj*) ve proje kökünden bileşen yolundan alınan proje adı.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="b6bb4-339">Örneğin, çerçeve ad alanına *{Project root}/Pages/Index.Razor* (*BlazorSample. csproj*) çözümleniyor `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="b6bb4-340">Bileşenler C# ad bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="b6bb4-341">`Index`Bu örnekteki bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="b6bb4-342">Aynı klasörde, *sayfalarda*.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="b6bb4-343">Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="b6bb4-344">Farklı bir ad alanında tanımlanan bileşenler, ' ın yönergesi kullanılarak kapsama alınır Razor [`@using`][2] .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="b6bb4-345">`NavMenu.razor` *BlazorSample/Shared/* klasöründe başka bir bileşen varsa, bileşeni `Index.razor` aşağıdaki ifadesiyle birlikte kullanılabilir [`@using`][2] :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="b6bb4-346">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`][2] yönergeyi gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="b6bb4-347">`global::`Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="b6bb4-348">Deyimleri [kullanan](/dotnet/csharp/language-reference/keywords/using-statement) (örneğin,) bileşenleri içeri aktarma `@using Foo = Bar` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-348">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="b6bb4-349">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="b6bb4-350">Örneğin, `@using BlazorSample` `NavMenu` ile bileşeni () ekleme ve başvuru `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-350">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="b6bb4-351">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="b6bb4-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="b6bb4-352">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="b6bb4-353">Değer `false` veya ise `null` , öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="b6bb4-354">Değer ise `true` , öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="b6bb4-355">Aşağıdaki örnekte, `IsCompleted` `checked` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini belirler:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="b6bb4-356">`IsCompleted`İse `true` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="b6bb4-357">`IsCompleted`İse `false` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="b6bb4-358">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="b6bb4-359">.NET türü bir olduğunda, [Aria-basılan](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)gıbı bazı HTML öznitelikleri düzgün şekilde çalışmaz `bool` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="b6bb4-360">Bu durumlarda, `string` yerine bir tür kullanın `bool` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="b6bb4-361">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="b6bb4-361">Raw HTML</span></span>

<span data-ttu-id="b6bb4-362">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="b6bb4-363">Ham HTML işlemek için, HTML içeriğini bir değerde sarın `MarkupString` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="b6bb4-364">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="b6bb4-365">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="b6bb4-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="b6bb4-366">Aşağıdaki örnek, `MarkupString` bir bileşenin işlenmiş çıktısına STATIK HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="b6bb4-367">Değerleri ve parametreleri basamaklama</span><span class="sxs-lookup"><span data-stu-id="b6bb4-367">Cascading values and parameters</span></span>

<span data-ttu-id="b6bb4-368">Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="b6bb4-369">Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="b6bb4-370">Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="b6bb4-371">Tema örneği</span><span class="sxs-lookup"><span data-stu-id="b6bb4-371">Theme example</span></span>

<span data-ttu-id="b6bb4-372">Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="b6bb4-373">*Uıthemeclasses/Themeınfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="b6bb4-374">Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="b6bb4-375"><xref:Microsoft.AspNetCore.Components.CascadingValue%601>Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-375">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="b6bb4-376">Örneğin, örnek uygulama, `ThemeInfo` uygulamanın düzenleriyle, özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir `@Body` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="b6bb4-377">`ButtonClass`öğesinin bir değeri, `btn-success` Düzen bileşeninde atanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="b6bb4-378">Tüm alt bileşenler bu özelliği basamaklı nesne aracılığıyla kullanabilir `ThemeInfo` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="b6bb4-379">`CascadingValuesParametersLayout`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="b6bb4-379">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="b6bb4-380">Basamaklı değerlerin kullanılması için, bileşenler özniteliği kullanarak Geçişli Parametreler bildirir [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-380">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="b6bb4-381">Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="b6bb4-382">Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="b6bb4-383">Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="b6bb4-384">`CascadingValuesParametersTheme`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="b6bb4-384">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="b6bb4-385">Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> her <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşene ve karşılık gelen özniteliğine benzersiz bir dize sağlayın [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-385">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="b6bb4-386">Aşağıdaki örnekte, iki <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşen adına göre farklı örneklerini basamakla `MyCascadingType` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-386">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="b6bb4-387">Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="b6bb4-388">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="b6bb4-388">TabSet example</span></span>

<span data-ttu-id="b6bb4-389">Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="b6bb4-390">Örneğin, örnek uygulamada aşağıdaki *Tabset* örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="b6bb4-391">Örnek uygulamada, `ITab` sekmelerin uygulandığı bir arabirim vardır:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="b6bb4-392">`CascadingValuesParametersTabSet`Bileşen, `TabSet` çeşitli bileşenleri içeren bileşenini kullanır `Tab` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="b6bb4-393">Alt `Tab` Bileşenler, öğesine açıkça parametre olarak aktarılmaz `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="b6bb4-394">Bunun yerine, alt `Tab` bileşenleri öğesinin alt içeriğinin bir parçasıdır `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="b6bb4-395">Bununla birlikte, `TabSet` `Tab` üst bilgileri ve etkin sekmeyi işleyebilmesi için her bileşen hakkında hala bilmeniz gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için bileşen, `TabSet` kendisini alt bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* `Tab` .</span><span class="sxs-lookup"><span data-stu-id="b6bb4-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="b6bb4-396">`TabSet`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="b6bb4-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="b6bb4-397">Alt `Tab` Bileşenler, kapsayan ' `TabSet` i basamaklı bir parametre olarak yakalar, bu nedenle bileşenler, `Tab` `TabSet` Bu sekmenin etkin olduğu ve koordinasyonu üzerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="b6bb4-398">`Tab`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="b6bb4-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="b6bb4-399">şablondan</span><span class="sxs-lookup"><span data-stu-id="b6bb4-399"> templates</span></span>

<span data-ttu-id="b6bb4-400">İşleme parçaları, Razor şablon sözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-400">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="b6bb4-401">Şablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-401"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="b6bb4-402">Aşağıdaki örnek, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> bir bileşeni doğrudan bir bileşende nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-402">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="b6bb4-403">Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="b6bb4-404">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="b6bb4-405">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="b6bb4-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="b6bb4-406">Bu yana Blazor , ölçeklenebilir vektör grafikleri (SVG) görüntüleri (*. SVG*) dahIl olmak üzere HTML, tarayıcı tarafından desteklenen görüntüler etiketi aracılığıyla desteklenir `<img>` :</span><span class="sxs-lookup"><span data-stu-id="b6bb4-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="b6bb4-407">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının (*. css*) CSS kurallarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="b6bb4-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="b6bb4-408">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="b6bb4-409">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına (*. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="b6bb4-410">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve [`@bind`][10] bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-410">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="b6bb4-411">Daha fazla bilgi için bkz. [Içindeki SVG desteği Blazor (DotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="b6bb4-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6bb4-412">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b6bb4-412">Additional resources</span></span>

* <span data-ttu-id="b6bb4-413"><xref:security/blazor/server/threat-mitigation>&ndash;Oluşturma Blazor hakkında rehberlik içerir Kaynak tükenmesi ile çekişmelidir sunucu uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="b6bb4-413"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
