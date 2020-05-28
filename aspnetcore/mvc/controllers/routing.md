---
<span data-ttu-id="972af-101">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-102">'Blazor'</span></span>
- <span data-ttu-id="972af-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-103">'Identity'</span></span>
- <span data-ttu-id="972af-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-105">'Razor'</span></span>
- <span data-ttu-id="972af-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="972af-107">ASP.NET Core denetleyici eylemlerine yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="972af-108">[Ryan şimdi ak](https://github.com/rynowak), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="972af-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="972af-109">ASP.NET Core denetleyicileri, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları [eylemlerle](#action)eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="972af-110">Rota şablonları:</span><span class="sxs-lookup"><span data-stu-id="972af-110">Routes templates:</span></span>

* <span data-ttu-id="972af-111">, Başlangıç kodunda veya özniteliklerde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="972af-112">URL yollarının [eylemlerle](#action)nasıl eşleştiğini betimleyen.</span><span class="sxs-lookup"><span data-stu-id="972af-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="972af-113">Bağlantıların URL 'Leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="972af-114">Oluşturulan bağlantılar genellikle yanıtlar halinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="972af-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="972af-115">Eylemler [genel olarak-yönlendirildi](#cr) veya [Attribute-yönlendirildi](#ar)' dir.</span><span class="sxs-lookup"><span data-stu-id="972af-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="972af-116">Bir yolun denetleyiciye veya [eyleme](#action) yerleştirilmesi, BT özniteliğinin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="972af-117">Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="972af-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="972af-118">Bu belge:</span><span class="sxs-lookup"><span data-stu-id="972af-118">This document:</span></span>

* <span data-ttu-id="972af-119">MVC ve yönlendirme arasındaki etkileşimleri açıklar:</span><span class="sxs-lookup"><span data-stu-id="972af-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="972af-120">Tipik MVC uygulamalarının yönlendirme özelliklerini kullanma şekli.</span><span class="sxs-lookup"><span data-stu-id="972af-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="972af-121">Her ikisini de içerir:</span><span class="sxs-lookup"><span data-stu-id="972af-121">Covers both:</span></span>
    * <span data-ttu-id="972af-122">Genellikle denetleyiciler ve görünümlerle kullanılan [genel olarak yönlendirme](#cr) .</span><span class="sxs-lookup"><span data-stu-id="972af-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="972af-123">REST API 'Leri ile kullanılan *öznitelik yönlendirme* .</span><span class="sxs-lookup"><span data-stu-id="972af-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="972af-124">Birincil olarak REST API 'Leri için yönlendirme ile ilgileniyorsanız, [REST API 'leri Için öznitelik yönlendirme](#ar) bölümüne atlayın.</span><span class="sxs-lookup"><span data-stu-id="972af-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="972af-125">Bkz. Gelişmiş yönlendirme ayrıntıları için [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="972af-126">ASP.NET Core 3,0 ' de eklenen varsayılan yönlendirme sisteminin Endpoint Routing olarak adlandırıldığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="972af-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="972af-127">Uyumluluk amaçlarıyla önceki yönlendirme sürümü ile denetleyicileri kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="972af-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="972af-128">Yönergeler için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="972af-129">Eski yönlendirme sistemindeki başvuru malzemeleri için [Bu belgenin 2,2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="972af-130">Geleneksel rotayı ayarlama</span><span class="sxs-lookup"><span data-stu-id="972af-130">Set up conventional route</span></span>

<span data-ttu-id="972af-131">`Startup.Configure`genellikle [geleneksel yönlendirme](#crd)kullanılırken aşağıdakine benzer bir kod içerir:</span><span class="sxs-lookup"><span data-stu-id="972af-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="972af-132">Çağrısı içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> tek bir yol oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="972af-133">Tek yol yol olarak adlandırılır `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-133">The single route is named `default` route.</span></span> <span data-ttu-id="972af-134">Denetleyiciler ve görünümler içeren çoğu uygulama, yola benzer bir rota şablonu kullanır `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="972af-135">REST API 'Leri [öznitelik yönlendirmeyi](#ar)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="972af-136">Yol şablonu `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="972af-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="972af-137">Şöyle bir URL yoluyla eşleşir`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="972af-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="972af-138">`{ controller = Products, action = Details, id = 5 }`Yolu simgeleştirileyerek yol değerlerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="972af-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="972af-139">Uygulamanın adlı bir denetleyici ve bir eylem varsa yol değerlerinin ayıklanması bir eşleşme ile sonuçlanır `ProductsController` `Details` :</span><span class="sxs-lookup"><span data-stu-id="972af-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="972af-140">`/Products/Details/5`model, `id = 5` parametresini olarak ayarlamak için değerini bağlar `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="972af-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="972af-141">Daha fazla ayrıntı için bkz. [model bağlama](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="972af-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="972af-142">`{controller=Home}``Home`Varsayılan olarak tanımlar `controller` .</span><span class="sxs-lookup"><span data-stu-id="972af-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="972af-143">`{action=Index}``Index`Varsayılan olarak tanımlar `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="972af-144">`?`İçindeki karakter, `{id?}` `id` isteğe bağlı olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="972af-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="972af-145">Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="972af-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="972af-146">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="972af-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="972af-147">URL yoluyla eşleşir `/` .</span><span class="sxs-lookup"><span data-stu-id="972af-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="972af-148">Yol değerlerini üretir `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="972af-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="972af-149">İçin değerleri `controller` ve `action` varsayılan değerleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="972af-150">`id`URL yolunda karşılık gelen bir kesim olmadığından değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="972af-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="972af-151">`/`yalnızca bir ve eylemi varsa eşleşir `HomeController` `Index` :</span><span class="sxs-lookup"><span data-stu-id="972af-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="972af-152">Önceki denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yolları için çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="972af-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="972af-153">URL yolu, `/` yönlendirme şablonu varsayılan `Home` denetleyicilerini ve eylemini kullanır `Index` .</span><span class="sxs-lookup"><span data-stu-id="972af-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="972af-154">URL yolu, `/Home` yönlendirme şablonu varsayılan eylemini kullanır `Index` .</span><span class="sxs-lookup"><span data-stu-id="972af-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="972af-155">Kolaylık yöntemi <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> :</span><span class="sxs-lookup"><span data-stu-id="972af-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="972af-156">Yerine</span><span class="sxs-lookup"><span data-stu-id="972af-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="972af-157">Yönlendirme, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ara yazılımı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="972af-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="972af-158">Denetleyicileri kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="972af-158">To use controllers:</span></span>
>
> * <span data-ttu-id="972af-159"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` [Öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için içinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="972af-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="972af-160"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> Hem [genel olarak yönlendirmeli](#cr) denetleyicileri hem de [öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için veya öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="972af-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="972af-161">Geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-161">Conventional routing</span></span>

<span data-ttu-id="972af-162">Geleneksel yönlendirme, denetleyiciler ve görünümlerle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="972af-163">`default`Yol:</span><span class="sxs-lookup"><span data-stu-id="972af-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="972af-164">, *geleneksel yönlendirmeye*bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="972af-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="972af-165">Bu, URL yolları için bir *kural* oluşturduğundan *geleneksel yönlendirme* olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="972af-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="972af-166">İlk yol segmenti, `{controller=Home}` , denetleyici adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="972af-167">İkinci kesim, `{action=Index}` , [eylem](#action) adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="972af-168">Üçüncü segment, `{id?}` isteğe bağlı olarak kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="972af-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="972af-169">`?`İçinde, `{id?}` isteğe bağlı yapar.</span><span class="sxs-lookup"><span data-stu-id="972af-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="972af-170">`id`bir model varlığına eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="972af-171">Bu `default` yolu kullanarak, URL yolu:</span><span class="sxs-lookup"><span data-stu-id="972af-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="972af-172">`/Products/List``ProductsController.List`eyleme eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="972af-173">`/Blog/Article/17`ile eşlenir `BlogController.Article` ve model genellikle `id` parametresini 17 ' ye bağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="972af-174">Bu eşleme:</span><span class="sxs-lookup"><span data-stu-id="972af-174">This mapping:</span></span>

* <span data-ttu-id="972af-175">**Yalnızca**denetleyiciyi ve [eylem](#action) adlarını temel alır.</span><span class="sxs-lookup"><span data-stu-id="972af-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="972af-176">Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel alan değildir.</span><span class="sxs-lookup"><span data-stu-id="972af-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="972af-177">Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, her eylem için yeni bir URL düzeniyle karşılaşmadan uygulamanın oluşturulmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="972af-178">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) stilinde eylemlere sahip bir uygulama için, denetleyiciler arasında URL 'ler için tutarlılık vardır:</span><span class="sxs-lookup"><span data-stu-id="972af-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="972af-179">Kodu basitleştirmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="972af-179">Helps simplify the code.</span></span>
* <span data-ttu-id="972af-180">Kullanıcı arabirimini daha öngörülebilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="972af-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="972af-181">`id`Önceki kodda, yol şablonu tarafından isteğe bağlı olarak tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="972af-182">Eylemler, URL 'nin bir parçası olarak belirtilen isteğe bağlı KIMLIK olmadan çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="972af-183">Genellikle, `id` URL 'den atlandığında:</span><span class="sxs-lookup"><span data-stu-id="972af-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="972af-184">`id`, `0` model bağlama tarafından olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="972af-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="972af-185">Veritabanında eşleşen bir varlık bulunamadı `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="972af-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="972af-186">[Öznitelik yönlendirme](#ar) , kimliği bazı eylemler için gerekli hale getirmek için ayrıntılı denetim sağlar ve diğerleri için değildir.</span><span class="sxs-lookup"><span data-stu-id="972af-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="972af-187">Kurala göre belgeler, `id` doğru kullanımlarda görünebilecekleri gibi isteğe bağlı parametreler içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="972af-188">Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="972af-189">Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="972af-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="972af-190">Temel ve açıklayıcı bir yönlendirme düzenini destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="972af-191">, UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="972af-192">Birçok Web UI uygulaması için tek yol şablonu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="972af-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="972af-193">Daha büyük Web Kullanıcı arabirimi uygulamaları için, sık sık gerekli olan [alanlarda](#areas) başka bir yol kullanın.</span><span class="sxs-lookup"><span data-stu-id="972af-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="972af-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="972af-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="972af-195">, Çağrıldığı sıraya göre kendi uç noktalarına otomatik olarak bir **sipariş** değeri atayın.</span><span class="sxs-lookup"><span data-stu-id="972af-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="972af-196">ASP.NET Core 3,0 ve üzeri için uç nokta yönlendirme:</span><span class="sxs-lookup"><span data-stu-id="972af-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="972af-197">Bir yol kavramı yoktur.</span><span class="sxs-lookup"><span data-stu-id="972af-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="972af-198">, Genişletilebilirlik yürütülmesi için sıralama garantisi sağlamaz, tüm uç noktalar bir kerede işlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="972af-199">İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının nasıl yapıldığını görmek için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin <xref:Microsoft.AspNetCore.Routing.Route> .</span><span class="sxs-lookup"><span data-stu-id="972af-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="972af-200">[Öznitelik yönlendirme](#ar) bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="972af-201">Birden çok geleneksel yollar</span><span class="sxs-lookup"><span data-stu-id="972af-201">Multiple conventional routes</span></span>

<span data-ttu-id="972af-202">[conventional routes](#cr) `UseEndpoints` Ve ' ye daha fazla çağrı eklenerek, içine birden çok geleneksel yol eklenebilir <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="972af-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="972af-203">Bunun yapılması, birden çok kural tanımlamayı veya belirli bir [eyleme](#action)adanmış geleneksel yollar eklemeyi sağlar; örneğin:</span><span class="sxs-lookup"><span data-stu-id="972af-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="972af-204">`blog`Önceki koddaki yol, **ayrılmış bir geleneksel yoldur**.</span><span class="sxs-lookup"><span data-stu-id="972af-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="972af-205">Ayrılmış bir geleneksel yol olarak adlandırılır çünkü:</span><span class="sxs-lookup"><span data-stu-id="972af-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="972af-206">[Geleneksel yönlendirme](#cr)kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="972af-207">Belirli bir [eyleme](#action)ayrılmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="972af-208">`controller`Ve `action` yol şablonunda parametre olarak görünmediği için `"blog/{*article}"` :</span><span class="sxs-lookup"><span data-stu-id="972af-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="972af-209">Yalnızca varsayılan değerleri olabilir `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="972af-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="972af-210">Bu yol her zaman eyleme eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="972af-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="972af-211">`/Blog`, `/Blog/Article` ve, `/Blog/{any-string}` Blog rotası ile eşleşen tek URL yollarıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="972af-212">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="972af-212">The preceding example:</span></span>

* <span data-ttu-id="972af-213">`blog`yol, önce eklendiğinden, rotadan eşleşme için daha yüksek önceliğe sahiptir `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="972af-214">Ve, URL 'nin bir parçası olarak bir makale adı olması gereken tipik bir [başlık](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="972af-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="972af-215">ASP.NET Core 3,0 ve üzeri sürümlerde yönlendirme:</span><span class="sxs-lookup"><span data-stu-id="972af-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="972af-216">*Yol*adlı bir kavram tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="972af-216">Define a concept called a *route*.</span></span> <span data-ttu-id="972af-217">`UseRouting`ara yazılım ardışık düzenine eşleşen rota ekler.</span><span class="sxs-lookup"><span data-stu-id="972af-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="972af-218">`UseRouting`Ara yazılım, uygulamada tanımlanan uç noktalar kümesine bakar ve isteğe bağlı olarak en iyi uç nokta eşleşmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="972af-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="972af-219">Veya gibi genişletilebilirlik yürütme sırası hakkında garanti sağlar <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="972af-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="972af-220">Bkz. yönlendirme ile ilgili başvuru malzemeleri için [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="972af-221">Geleneksel yönlendirme sırası</span><span class="sxs-lookup"><span data-stu-id="972af-221">Conventional routing order</span></span>

<span data-ttu-id="972af-222">Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyicinin bir bileşimiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="972af-223">Bu, geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="972af-224">,, Ve kullanarak yollar ekleme, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> çağırdıkları sıraya göre bitiş noktalarına otomatik olarak bir sipariş değeri atar.</span><span class="sxs-lookup"><span data-stu-id="972af-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="972af-225">Daha önce görüntülenen bir rotadaki eşleşmelerin önceliği daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="972af-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="972af-226">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="972af-227">Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="972af-228">Catch-all yol parametrelerine sahip [adanmış geleneksel yollar](#dcr) `{*article}` , bir yol çok uzun sürebilir [greedy](xref:fundamentals/routing#greedy), yani diğer yollarla eşleştirmek istediğiniz URL 'lerle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="972af-229">Doyumsuz yollarını daha sonra yol tablosuna yerleştirerek doyumsuz eşleşmelerini önleyin.</span><span class="sxs-lookup"><span data-stu-id="972af-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="972af-230">Belirsiz eylemleri çözme</span><span class="sxs-lookup"><span data-stu-id="972af-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="972af-231">Yönlendirme ile iki uç nokta eşleşmesi durumunda, yönlendirme aşağıdakilerden birini yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="972af-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="972af-232">En iyi aday ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="972af-232">Choose the best candidate.</span></span>
* <span data-ttu-id="972af-233">Bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-233">Throw an exception.</span></span>

<span data-ttu-id="972af-234">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="972af-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="972af-235">Yukarıdaki denetleyici, eşleşen iki eylemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="972af-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="972af-236">URL yolu`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="972af-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="972af-237">Veri yönlendirin `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="972af-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="972af-238">Bu, MVC denetleyicileri için tipik bir modeldir:</span><span class="sxs-lookup"><span data-stu-id="972af-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="972af-239">`Edit(int)`bir ürünü düzenlemek için bir form görüntüler.</span><span class="sxs-lookup"><span data-stu-id="972af-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="972af-240">`Edit(int, Product)`Postalanan formu işler.</span><span class="sxs-lookup"><span data-stu-id="972af-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="972af-241">Doğru yolu çözümlemek için:</span><span class="sxs-lookup"><span data-stu-id="972af-241">To resolve the correct route:</span></span>

* <span data-ttu-id="972af-242">`Edit(int, Product)`istek bir HTTP olduğunda seçilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="972af-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="972af-243">`Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir.</span><span class="sxs-lookup"><span data-stu-id="972af-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="972af-244">`Edit(int)`genellikle aracılığıyla çağrılır `GET` .</span><span class="sxs-lookup"><span data-stu-id="972af-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="972af-245">, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` İsteğin HTTP yöntemine göre seçim yapabilmesi için yönlendirme için verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="972af-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="972af-246">, `HttpPostAttribute` `Edit(int, Product)` Daha iyi bir eşleşme yapar `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="972af-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="972af-247">Gibi özniteliklerin rolünü anlamanız önemlidir `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="972af-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="972af-248">Benzer öznitelikler diğer [http fiilleri](#verb)için tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="972af-249">[Geleneksel yönlendirmesinde](#cr), bir görüntüleme formu, gönder formu iş akışının bir parçası olduklarında aynı eylem adını kullanmak yaygın olarak kullanılan bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="972af-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="972af-250">Örneğin, bkz. [Iki düzenleme eylemi yöntemini İnceleme](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="972af-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="972af-251">Yönlendirme bir en iyi aday seçebilirse, <xref:System.Reflection.AmbiguousMatchException> birden fazla eşleşen uç noktayı listeleyerek bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="972af-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="972af-252">Geleneksel yol adları</span><span class="sxs-lookup"><span data-stu-id="972af-252">Conventional route names</span></span>

<span data-ttu-id="972af-253">Dizeler `"blog"` ve `"default"` Aşağıdaki örneklerde geleneksel yol adları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="972af-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="972af-254">Yol adları, yola mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="972af-254">The route names give the route a logical name.</span></span> <span data-ttu-id="972af-255">Adlandırılmış yol, URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="972af-256">Adlandırılmış bir yol kullanmak, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="972af-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="972af-257">Yol adları benzersiz uygulama genelinde olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="972af-258">Yol adları:</span><span class="sxs-lookup"><span data-stu-id="972af-258">Route names:</span></span>

* <span data-ttu-id="972af-259">İsteklerin URL 'siyle eşleşmesi veya işlenmesi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="972af-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="972af-260">Yalnızca URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-260">Are used only for URL generation.</span></span>

<span data-ttu-id="972af-261">Yol adı kavramı [ıendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)olarak yönlendirme ile temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="972af-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="972af-262">Terimler **yol adı** ve **uç nokta adı**:</span><span class="sxs-lookup"><span data-stu-id="972af-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="972af-263">Değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-263">Are interchangeable.</span></span>
* <span data-ttu-id="972af-264">Belgede kullanılan ve kod, açıklanan API 'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="972af-265">REST API 'Leri için öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="972af-266">REST API 'Leri, uygulamanın işlevselliğini [http fiilleri](#verb)tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="972af-267">Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="972af-268">Aşağıdaki `StartUp.Configure` kod, bir REST API için tipik bir sonraki örnekte kullanılır:</span><span class="sxs-lookup"><span data-stu-id="972af-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="972af-269">Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` öznitelik yönlendirmeli denetleyicileri eşlemek için içinde çağırılır.</span><span class="sxs-lookup"><span data-stu-id="972af-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="972af-270">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="972af-270">In the following example:</span></span>

* <span data-ttu-id="972af-271">Önceki `Configure` yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="972af-272">`HomeController`Varsayılan geleneksel yolun eşleşdiküyle benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="972af-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="972af-273">Eylem,, `HomeController.Index` veya URL yollarından herhangi biri için çalıştırılır `/` `/Home` `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="972af-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="972af-274">Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasında bir temel programlama farkı vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="972af-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="972af-275">Öznitelik yönlendirme için bir yol belirtmek için daha fazla giriş gerekir.</span><span class="sxs-lookup"><span data-stu-id="972af-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="972af-276">Geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler.</span><span class="sxs-lookup"><span data-stu-id="972af-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="972af-277">Ancak, öznitelik yönlendirme izin verir ve her [eyleme](#action)hangi rota şablonlarının uygulanacağını kesin olarak denetler.</span><span class="sxs-lookup"><span data-stu-id="972af-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="972af-278">Öznitelik yönlendirme ile, denetleyici ve eylem adları, [belirteç değiştirme](#routing-token-replacement-templates-ref-label) kullanılmadığı takdirde, eylemin eşleştirildiği hiçbir parçasını oynar.</span><span class="sxs-lookup"><span data-stu-id="972af-278">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="972af-279">Aşağıdaki örnek, önceki örnekle aynı URL 'Lerle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="972af-279">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="972af-280">Aşağıdaki kod, ve için belirteç yerini `action` kullanır `controller` :</span><span class="sxs-lookup"><span data-stu-id="972af-280">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="972af-281">Aşağıdaki kod `[Route("[controller]/[action]")]` Denetleyici için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="972af-281">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="972af-282">Yukarıdaki kodda, `Index` Yöntem şablonlarının sonuna `/` veya yol şablonlarına eklenmiş olması gerekir `~/` .</span><span class="sxs-lookup"><span data-stu-id="972af-282">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="972af-283">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="972af-283">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="972af-284">Rota şablonu seçimi hakkında bilgi için bkz. [route Template önceliği](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="972af-284">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="972af-285">Ayrılmış yönlendirme adları</span><span class="sxs-lookup"><span data-stu-id="972af-285">Reserved routing names</span></span>

<span data-ttu-id="972af-286">Aşağıdaki anahtar sözcükler, denetleyiciler veya sayfalar kullanılırken ayrılmış yol parametresi adlarıdır Razor :</span><span class="sxs-lookup"><span data-stu-id="972af-286">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="972af-287">`page`Öznitelik yönlendirme ile yol parametresi olarak kullanmak yaygın bir hatadır.</span><span class="sxs-lookup"><span data-stu-id="972af-287">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="972af-288">Bunun yapılması, URL oluşturma ile tutarsız ve kafa karıştırıcı davranışa neden olur.</span><span class="sxs-lookup"><span data-stu-id="972af-288">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="972af-289">Özel parametre adları URL oluşturma tarafından bir URL oluşturma işleminin bir sayfaya mı yoksa bir denetleyiciye mi başvurduğunu belirlemekte kullanılır Razor .</span><span class="sxs-lookup"><span data-stu-id="972af-289">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="972af-290">HTTP fiili şablonları</span><span class="sxs-lookup"><span data-stu-id="972af-290">HTTP verb templates</span></span>

<span data-ttu-id="972af-291">ASP.NET Core aşağıdaki HTTP fiili şablonlarına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="972af-291">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="972af-292">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-292">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="972af-293">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-293">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="972af-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="972af-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="972af-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="972af-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="972af-298">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="972af-298">Route templates</span></span>

<span data-ttu-id="972af-299">ASP.NET Core aşağıdaki yol şablonlarına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="972af-299">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="972af-300">Tüm [http fiili şablonları](#verb) rota şablonlarıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-300">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="972af-301">[Yolu](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="972af-301">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="972af-302">Http fiili öznitelikleriyle öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-302">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="972af-303">Aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="972af-303">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="972af-304">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="972af-304">In the preceding code:</span></span>

* <span data-ttu-id="972af-305">Her eylem `[HttpGet]` yalnızca http get istekleriyle eşleştirmeyi kısıtlayan özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-305">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="972af-306">`GetProduct`Eylem, şablonu içerir `"{id}"` , bu nedenle `id` `"api/[controller]"` denetleyicideki şablonun sonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="972af-306">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="972af-307">Yöntemler şablonu `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="972af-307">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="972af-308">Bu nedenle bu eylem yalnızca `/api/test2/xyz` ,, `/api/test2/123` vb. için get istekleri ile eşleşir `/api/test2/{any string}` .</span><span class="sxs-lookup"><span data-stu-id="972af-308">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="972af-309">`GetIntProduct`Eylem `"int/{id:int}")` şablonu içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-309">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="972af-310">`:int`Şablonun kısmı, `id` yol değerlerini bir tamsayıya dönüştürülebileceği dizelere kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="972af-310">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="972af-311">Bir GET isteği `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="972af-311">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="972af-312">Bu eylemle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="972af-312">Doesn't match this action.</span></span>
  * <span data-ttu-id="972af-313">Bir [404 bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="972af-313">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="972af-314">`GetInt2Product`Eylem şablonda bulunur `{id}` , ancak `id` bir tamsayıya dönüştürülemeyen değerlerle sınırlandırmaz.</span><span class="sxs-lookup"><span data-stu-id="972af-314">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="972af-315">Bir GET isteği `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="972af-315">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="972af-316">Bu rota ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-316">Matches this route.</span></span>
  * <span data-ttu-id="972af-317">Model bağlama `abc` bir tamsayıya dönüştürülemez.</span><span class="sxs-lookup"><span data-stu-id="972af-317">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="972af-318">`id`Yönteminin parametresi tamsayıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-318">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="972af-319">Model bağlama bir tamsayıya dönüştürülemediğinden, [400 hatalı bir istek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür `abc` .</span><span class="sxs-lookup"><span data-stu-id="972af-319">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="972af-320">Öznitelik yönlendirme,, <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> ve gibi öznitelikleri <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> kullanabilir <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="972af-320">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="972af-321">Tüm [http fiili](#verb) öznitelikleri bir yol şablonunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="972af-321">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="972af-322">Aşağıdaki örnekte, aynı yol şablonuyla eşleşen iki eylem gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="972af-322">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="972af-323">URL yolu kullanılarak `/products3` :</span><span class="sxs-lookup"><span data-stu-id="972af-323">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="972af-324">`MyProductsController.ListProducts`Eylem [http fiili](#verb) olduğunda çalışır `GET` .</span><span class="sxs-lookup"><span data-stu-id="972af-324">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="972af-325">`MyProductsController.CreateProduct`Eylem [http fiili](#verb) olduğunda çalışır `POST` .</span><span class="sxs-lookup"><span data-stu-id="972af-325">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="972af-326">Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul ettiğinden bir eylem yönteminde kullanmanız gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="972af-326">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="972af-327">API 'nizin neleri desteklediği hakkında kesin olması için, daha özel [http fiili özniteliği](#verb) kullanmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="972af-327">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="972af-328">REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-328">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="972af-329">REST API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-329">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="972af-330">Bu, örneğin, aynı mantıksal kaynaktaki al ve postala gibi birçok işlemin aynı URL 'YI kullanması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-330">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="972af-331">Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-331">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="972af-332">Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="972af-332">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="972af-333">Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:</span><span class="sxs-lookup"><span data-stu-id="972af-333">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="972af-334">`Products2ApiController.GetProduct(int)`Eylem:</span><span class="sxs-lookup"><span data-stu-id="972af-334">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="972af-335">, Şunun gibi URL yoluyla çalıştırılır`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="972af-335">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="972af-336">URL yoluyla çalıştırılmadı `/products2` .</span><span class="sxs-lookup"><span data-stu-id="972af-336">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="972af-337">[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-337">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="972af-338">Daha fazla bilgi için bkz. [desteklenen istek içerik türlerini tüketir özniteliğiyle tanımlama](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="972af-338">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="972af-339">Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-339">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="972af-340">Hakkında daha fazla bilgi için `[ApiController]` bkz. [Apicontroller özniteliği](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="972af-340">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="972af-341">Yönlendirme adı</span><span class="sxs-lookup"><span data-stu-id="972af-341">Route name</span></span>

<span data-ttu-id="972af-342">Aşağıdaki kod, bir yol adı tanımlar `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="972af-342">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="972af-343">Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-343">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="972af-344">Yol adları:</span><span class="sxs-lookup"><span data-stu-id="972af-344">Route names:</span></span>

* <span data-ttu-id="972af-345">Yönlendirmenin URL eşleşen davranışı üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="972af-345">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="972af-346">Yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-346">Are only used for URL generation.</span></span>

<span data-ttu-id="972af-347">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-347">Route names must be unique application-wide.</span></span>

<span data-ttu-id="972af-348">Önceki kodu, `id` parametreyi isteğe bağlı () olarak tanımlayan geleneksel varsayılan rotayla kontrast `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="972af-348">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="972af-349">API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .</span><span class="sxs-lookup"><span data-stu-id="972af-349">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="972af-350">Öznitelik yollarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="972af-350">Combining attribute routes</span></span>

<span data-ttu-id="972af-351">Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-351">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="972af-352">Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir.</span><span class="sxs-lookup"><span data-stu-id="972af-352">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="972af-353">Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-353">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="972af-354">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="972af-354">In the preceding example:</span></span>

* <span data-ttu-id="972af-355">URL yolu `/products` eşleşiyor`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="972af-355">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="972af-356">URL yolu `/products/5` eşleşiyor `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="972af-356">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="972af-357">Bu eylemlerin her ikisi de, özniteliğiyle işaretlendiğinden yalnızca HTTP ile eşleşir `GET` `[HttpGet]` .</span><span class="sxs-lookup"><span data-stu-id="972af-357">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="972af-358">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="972af-358">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="972af-359">Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolları kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-359">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="972af-360">Aşağıdaki tabloda `[Route]` önceki koddaki öznitelikler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="972af-360">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="972af-361">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="972af-361">Attribute</span></span>               | <span data-ttu-id="972af-362">İle birleştirir`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="972af-362">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="972af-363">Rota şablonunu tanımlar</span><span class="sxs-lookup"><span data-stu-id="972af-363">Defines route template</span></span> |
| ---
<span data-ttu-id="972af-364">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-364">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-365">'Blazor'</span></span>
- <span data-ttu-id="972af-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-366">'Identity'</span></span>
- <span data-ttu-id="972af-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-368">'Razor'</span></span>
- <span data-ttu-id="972af-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-370">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-370">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-371">'Blazor'</span></span>
- <span data-ttu-id="972af-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-372">'Identity'</span></span>
- <span data-ttu-id="972af-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-374">'Razor'</span></span>
- <span data-ttu-id="972af-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-376">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-376">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-377">'Blazor'</span></span>
- <span data-ttu-id="972af-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-378">'Identity'</span></span>
- <span data-ttu-id="972af-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-380">'Razor'</span></span>
- <span data-ttu-id="972af-381">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-382">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-382">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-383">'Blazor'</span></span>
- <span data-ttu-id="972af-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-384">'Identity'</span></span>
- <span data-ttu-id="972af-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-386">'Razor'</span></span>
- <span data-ttu-id="972af-387">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-388">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-388">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-389">'Blazor'</span></span>
- <span data-ttu-id="972af-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-390">'Identity'</span></span>
- <span data-ttu-id="972af-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-392">'Razor'</span></span>
- <span data-ttu-id="972af-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-394">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-394">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-395">'Blazor'</span></span>
- <span data-ttu-id="972af-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-396">'Identity'</span></span>
- <span data-ttu-id="972af-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-398">'Razor'</span></span>
- <span data-ttu-id="972af-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-399">'SignalR' uid:</span></span> 

<span data-ttu-id="972af-400">--------- | ---title: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-400">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-401">'Blazor'</span></span>
- <span data-ttu-id="972af-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-402">'Identity'</span></span>
- <span data-ttu-id="972af-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-404">'Razor'</span></span>
- <span data-ttu-id="972af-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-406">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-406">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-407">'Blazor'</span></span>
- <span data-ttu-id="972af-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-408">'Identity'</span></span>
- <span data-ttu-id="972af-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-410">'Razor'</span></span>
- <span data-ttu-id="972af-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-412">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-412">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-413">'Blazor'</span></span>
- <span data-ttu-id="972af-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-414">'Identity'</span></span>
- <span data-ttu-id="972af-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-416">'Razor'</span></span>
- <span data-ttu-id="972af-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-418">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-418">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-419">'Blazor'</span></span>
- <span data-ttu-id="972af-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-420">'Identity'</span></span>
- <span data-ttu-id="972af-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-422">'Razor'</span></span>
- <span data-ttu-id="972af-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-423">'SignalR' uid:</span></span> 

<span data-ttu-id="972af-424">------ | ---title: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-424">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-425">'Blazor'</span></span>
- <span data-ttu-id="972af-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-426">'Identity'</span></span>
- <span data-ttu-id="972af-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-428">'Razor'</span></span>
- <span data-ttu-id="972af-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="972af-430">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="972af-430">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="972af-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="972af-431">'Blazor'</span></span>
- <span data-ttu-id="972af-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="972af-432">'Identity'</span></span>
- <span data-ttu-id="972af-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="972af-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="972af-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="972af-434">'Razor'</span></span>
- <span data-ttu-id="972af-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="972af-435">'SignalR' uid:</span></span> 

<span data-ttu-id="972af-436">----- | | `[Route("")]` | Evet | `"Home"` |
| `[Route("Index")]` | Evet | `"Home/Index"` |
| `[Route("/")]` | **Hayır** | `""` |
 | `[Route("About")]` | Evet | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="972af-436">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="972af-437">Öznitelik yolu sırası</span><span class="sxs-lookup"><span data-stu-id="972af-437">Attribute route order</span></span>

<span data-ttu-id="972af-438">Yönlendirme bir ağaç oluşturur ve aynı anda tüm uç noktaları eşleştirir:</span><span class="sxs-lookup"><span data-stu-id="972af-438">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="972af-439">Yol girdileri ideal bir sıralamaya yerleştirilmiş gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="972af-439">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="972af-440">En özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.</span><span class="sxs-lookup"><span data-stu-id="972af-440">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="972af-441">Örneğin, gibi bir öznitelik yolu, `blog/search/{topic}` gibi bir öznitelik rotasına göre daha özgüdür `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="972af-441">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="972af-442">`blog/search/{topic}`Daha belirgin olduğundan, yolun önceliği daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="972af-442">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="972af-443">[Geleneksel yönlendirmeyi](#cr)kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.</span><span class="sxs-lookup"><span data-stu-id="972af-443">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="972af-444">Öznitelik yolları özelliği kullanarak bir sıra yapılandırabilir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="972af-444">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="972af-445">Tüm Framework 'ün [yol öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dahil edilmiştir `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-445">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="972af-446">Yollar, özelliğin artan sıralamasına göre işlenir `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-446">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="972af-447">Varsayılan sıra `0` .</span><span class="sxs-lookup"><span data-stu-id="972af-447">The default order is `0`.</span></span> <span data-ttu-id="972af-448">Bir `Order = -1` siparişi ayarlamadığı rotalardan önce çalıştırmaları kullanarak bir rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="972af-448">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="972af-449">`Order = 1`Varsayılan yol sıralaması sonrasında çalıştırmaları kullanarak bir rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="972af-449">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="972af-450">**Avoid** Uygulamasına bağlı kaçının `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-450">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="972af-451">Bir uygulamanın URL 'SI alanı, doğru şekilde yönlendirmek için açık sıra değerleri gerektiriyorsa, bu durumda istemciler de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-451">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="972af-452">Genel olarak, öznitelik yönlendirme URL ile eşleşen doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="972af-452">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="972af-453">URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, geçersiz kılma olarak bir yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-453">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="972af-454">Her ikisi de eşleşen yolu tanımlayan aşağıdaki iki denetleyicisi göz önünde bulundurun `/home` :</span><span class="sxs-lookup"><span data-stu-id="972af-454">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="972af-455">`/home`Yukarıdaki kodla istemek şuna benzer bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="972af-455">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="972af-456">`Order`Yol özniteliklerinden birine ekleme belirsizliği çözer:</span><span class="sxs-lookup"><span data-stu-id="972af-456">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="972af-457">Yukarıdaki kodla `/home` `HomeController.Index` bitiş noktasını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="972af-457">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="972af-458">Öğesine ulaşmak için `MyDemoController.MyIndex` `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="972af-458">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="972af-459">**Note**:</span><span class="sxs-lookup"><span data-stu-id="972af-459">**Note**:</span></span>

* <span data-ttu-id="972af-460">Yukarıdaki kod bir örnek veya kötü yönlendirme tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="972af-460">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="972af-461">Özelliği göstermek için kullanılmıştı `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-461">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="972af-462">`Order`Özelliği yalnızca belirsizlik çözümleniyor, bu şablon eşleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="972af-462">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="972af-463">Şablonu kaldırmak daha iyi olacaktır `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="972af-463">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="972af-464">Bkz. [ Razor sayfa yönlendirme ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) sayfalarla rota sıralaması hakkında bilgi için rota sırası Razor .</span><span class="sxs-lookup"><span data-stu-id="972af-464">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="972af-465">Bazı durumlarda, belirsiz yollarla bir HTTP 500 hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="972af-465">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="972af-466">Hangi uç noktaların neden olduğunu görmek için [günlük kaydını](xref:fundamentals/logging/index) kullanın `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="972af-466">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="972af-467">Yönlendirme şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]</span><span class="sxs-lookup"><span data-stu-id="972af-467">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="972af-468">Daha kolay olması için, öznitelik rotaları, aşağıdakilerden birine bir belirteç ekleyerek ayrılmış yol parametrelerine yönelik belirteç değişimini destekler:</span><span class="sxs-lookup"><span data-stu-id="972af-468">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="972af-469">Köşeli ayraçlar:`[]`</span><span class="sxs-lookup"><span data-stu-id="972af-469">Square brackets: `[]`</span></span>
* <span data-ttu-id="972af-470">Küme ayraçları:`{}`</span><span class="sxs-lookup"><span data-stu-id="972af-470">Curly braces: `{}`</span></span>

<span data-ttu-id="972af-471">, Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="972af-471">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="972af-472">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="972af-472">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="972af-473">Eşleştir`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="972af-473">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="972af-474">Eşleştir`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="972af-474">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="972af-475">Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-475">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="972af-476">Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="972af-476">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="972af-477">Öznitelik rotaları de devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-477">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="972af-478">Bu, belirteç değiştirme ile güçlü bir şekilde birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-478">This is powerful combined with token replacement.</span></span> <span data-ttu-id="972af-479">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-479">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="972af-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir:</span><span class="sxs-lookup"><span data-stu-id="972af-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="972af-481">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-481">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="972af-482">Her eylem için benzersiz bir yol adı üretir.</span><span class="sxs-lookup"><span data-stu-id="972af-482">generates a unique route name for each action.</span></span>

<span data-ttu-id="972af-483">Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .</span><span class="sxs-lookup"><span data-stu-id="972af-483">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="972af-484">Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="972af-484">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="972af-485">Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-485">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="972af-486">Bir parametre transformatörü <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="972af-486">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="972af-487">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini şu şekilde değiştirir `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="972af-487">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="972af-488">, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Şu şekilde bir uygulama modeli kuralıdır:</span><span class="sxs-lookup"><span data-stu-id="972af-488">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="972af-489">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="972af-489">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="972af-490">Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="972af-490">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="972af-491">Önceki `ListAll` Yöntem eşleşir `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="972af-491">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="972af-492">, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="972af-492">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="972af-493">Bilgi tanımı için bkz. [Mdıdn Web docs](https://developer.mozilla.org/docs/Glossary/Slug) for the başlık.</span><span class="sxs-lookup"><span data-stu-id="972af-493">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="972af-494">Birden çok öznitelik yolu</span><span class="sxs-lookup"><span data-stu-id="972af-494">Multiple attribute routes</span></span>

<span data-ttu-id="972af-495">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-495">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="972af-496">Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel yolun davranışını taklit etmek olur:</span><span class="sxs-lookup"><span data-stu-id="972af-496">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="972af-497">Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biri ile birleştiribileceği anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="972af-497">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="972af-498">Tüm [http fiili](#verb) yol kısıtlamaları uygulanır `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="972af-498">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="972af-499">Uygulayan birden çok yol özniteliği <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> bir eyleme yerleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="972af-499">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="972af-500">Her eylem kısıtlaması, denetleyiciye uygulanan rota şablonuyla birlikte birleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-500">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="972af-501">Eylemlerde birden çok yolun kullanılması yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmanız daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="972af-501">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="972af-502">**Yalnızca** gerektiğinde, var olan istemcileri desteklemek için, eylemlerde birden çok yol kullanın.</span><span class="sxs-lookup"><span data-stu-id="972af-502">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="972af-503">Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme</span><span class="sxs-lookup"><span data-stu-id="972af-503">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="972af-504">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-504">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="972af-505">Yukarıdaki kodda, `[HttpPost("product/{id:int}")]` bir yol kısıtlaması uygular.</span><span class="sxs-lookup"><span data-stu-id="972af-505">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="972af-506">`ProductsController.ShowProduct`Eylem yalnızca, gıbı URL yollarıyla eşleştirilir `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="972af-506">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="972af-507">Yol şablonu bölümü, `{id:int}` Bu segmenti yalnızca tamsayılarla kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="972af-507">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="972af-508">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="972af-508">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="972af-509">Iroutetemplateprovider kullanarak özel yol öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="972af-509">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="972af-510">Tüm [Rota öznitelikleri](#rt) uygular <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="972af-510">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="972af-511">ASP.NET Core çalışma zamanı:</span><span class="sxs-lookup"><span data-stu-id="972af-511">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="972af-512">Uygulama başladığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar.</span><span class="sxs-lookup"><span data-stu-id="972af-512">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="972af-513">`IRouteTemplateProvider`İlk yol kümesini oluşturmak için uygulayan öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-513">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="972af-514">`IRouteTemplateProvider`Özel yol özniteliklerini tanımlamak için uygulayın.</span><span class="sxs-lookup"><span data-stu-id="972af-514">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="972af-515">Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="972af-515">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="972af-516">Önceki `Get` Yöntem döndürülür `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="972af-516">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="972af-517">Öznitelik yollarını özelleştirmek için uygulama modelini kullanın</span><span class="sxs-lookup"><span data-stu-id="972af-517">Use application model to customize attribute routes</span></span>

<span data-ttu-id="972af-518">Uygulama modeli:</span><span class="sxs-lookup"><span data-stu-id="972af-518">The application model:</span></span>

* <span data-ttu-id="972af-519">, Başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-519">Is an object model created at startup.</span></span>
* <span data-ttu-id="972af-520">Bir uygulamadaki eylemleri yönlendirmek ve yürütmek için ASP.NET Core tarafından kullanılan tüm meta verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-520">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="972af-521">Uygulama modeli, yol özniteliklerinden toplanan tüm verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-521">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="972af-522">Yol özniteliklerinin verileri uygulama tarafından sağlanır `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="972af-522">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="972af-523">Adlandır</span><span class="sxs-lookup"><span data-stu-id="972af-523">Conventions:</span></span>

* <span data-ttu-id="972af-524">, Yönlendirmenin nasıl davranacağını özelleştirmek için uygulama modelini değiştirmek üzere yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-524">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="972af-525">Uygulama başlangıcında okundu.</span><span class="sxs-lookup"><span data-stu-id="972af-525">Are read at app startup.</span></span>

<span data-ttu-id="972af-526">Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin temel bir örneği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="972af-526">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="972af-527">Aşağıdaki kod, rotaları projenin klasör yapısıyla kabaca bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-527">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="972af-528">Aşağıdaki kod, `namespace` kuralı yönlendirilen denetleyicilere uygulanmasını engeller:</span><span class="sxs-lookup"><span data-stu-id="972af-528">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="972af-529">Örneğin, aşağıdaki denetleyici kullanmaz `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="972af-529">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="972af-530">`NamespaceRoutingConvention.Apply`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="972af-530">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="972af-531">Denetleyici öznitelik yönlendirmemişse hiçbir şey yapmaz.</span><span class="sxs-lookup"><span data-stu-id="972af-531">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="972af-532">Temel kaldırılmış olarak, denetleyici şablonunu öğesine göre ayarlar `namespace` `namespace` .</span><span class="sxs-lookup"><span data-stu-id="972af-532">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="972af-533">, `NamespaceRoutingConvention` ' De uygulanabilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="972af-533">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="972af-534">Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="972af-534">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="972af-535">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="972af-535">In the preceding code:</span></span>

* <span data-ttu-id="972af-536">Temel `namespace` `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="972af-536">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="972af-537">Önceki denetleyicinin tam adı `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="972af-537">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="972af-538">, `NamespaceRoutingConvention` Denetleyiciler şablonunu olarak ayarlar `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="972af-538">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="972af-539">, `NamespaceRoutingConvention` Bir denetleyiciye bir öznitelik olarak da uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="972af-539">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="972af-540">Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-540">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="972af-541">ASP.NET Core uygulamalar geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-541">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="972af-542">Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.</span><span class="sxs-lookup"><span data-stu-id="972af-542">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="972af-543">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-543">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="972af-544">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-544">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="972af-545">Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-545">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="972af-546">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki **Tüm** eylemlerin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-546">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="972af-547">Öznitelik yönlendirme ve geleneksel yönlendirme aynı yönlendirme altyapısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-547">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="972af-548">URL oluşturma ve ortam değerleri</span><span class="sxs-lookup"><span data-stu-id="972af-548">URL Generation and ambient values</span></span>

<span data-ttu-id="972af-549">Uygulamalar, eylemlere URL bağlantıları oluşturmak için yönlendirme URL 'SI oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-549">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="972af-550">URL 'Leri oluşturmak, kod daha sağlam ve sürdürülebilir hale getirmek için sorunsuz kodlama URL 'Lerini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="972af-550">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="972af-551">Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturma özelliğinin nasıl çalıştığına ilişkin temel bilgileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="972af-551">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="972af-552">URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-552">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="972af-553"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki altyapının temel öğesidir.</span><span class="sxs-lookup"><span data-stu-id="972af-553">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="972af-554">Bir örneği, `IUrlHelper` `Url` denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-554">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="972af-555">Aşağıdaki örnekte, `IUrlHelper` arabirimi `Controller.Url` başka bir eyleme bir URL oluşturmak için özelliği aracılığıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-555">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="972af-556">Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkeninin DEĞERI URL yol dizesidir `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="972af-556">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="972af-557">Bu URL yolu, yönlendirme tarafından birleştirerek oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="972af-557">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="972af-558">Geçerli istekten, **ortam değerleri**olarak adlandırılan rota değerleri.</span><span class="sxs-lookup"><span data-stu-id="972af-558">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="972af-559">`Url.Action`Bu değerleri yol şablonuna geçirilen ve yerine geçen değerler:</span><span class="sxs-lookup"><span data-stu-id="972af-559">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="972af-560">Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-560">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="972af-561">Bir değere sahip olmayan bir rota parametresi şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="972af-561">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="972af-562">Bir varsayılan değer varsa, bu değeri kullanın.</span><span class="sxs-lookup"><span data-stu-id="972af-562">Use a default value if it has one.</span></span>
* <span data-ttu-id="972af-563">İsteğe bağlı ise atlanır.</span><span class="sxs-lookup"><span data-stu-id="972af-563">Be skipped if it's optional.</span></span> <span data-ttu-id="972af-564">Örneğin, `id` Rota şablonundan `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="972af-564">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="972af-565">Herhangi bir gerekli yol parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="972af-565">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="972af-566">Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.</span><span class="sxs-lookup"><span data-stu-id="972af-566">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="972af-567">Yukarıdaki örnekte `Url.Action` [geleneksel yönlendirme](#cr)varsayılır.</span><span class="sxs-lookup"><span data-stu-id="972af-567">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="972af-568">URL oluşturma, [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır, ancak kavramlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-568">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="972af-569">Geleneksel yönlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="972af-569">With conventional routing:</span></span>

* <span data-ttu-id="972af-570">Rota değerleri bir şablonu genişletmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-570">The route values are used to expand a template.</span></span>
* <span data-ttu-id="972af-571">İçin yol değerleri `controller` ve `action` genellikle bu şablonda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="972af-571">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="972af-572">Bu, yönlendirme ile eşleşen URL 'Ler bir kurala bağlı olduğundan, bu işe yarar.</span><span class="sxs-lookup"><span data-stu-id="972af-572">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="972af-573">Aşağıdaki örnek öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="972af-573">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="972af-574">`Source`Yukarıdaki koddaki eylem oluşturulur `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="972af-574">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="972af-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>ASP.NET Core 3,0 ' ye alternatif olarak eklenmiştir `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="972af-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="972af-576">`LinkGenerator`benzer ancak daha esnek işlevler sunar.</span><span class="sxs-lookup"><span data-stu-id="972af-576">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="972af-577">Üzerindeki her `IUrlHelper` bir yöntemi, buna karşılık gelen bir yöntem ailesini `LinkGenerator` de içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-577">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="972af-578">Eylem adına göre URL 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-578">Generating URLs by action name</span></span>

<span data-ttu-id="972af-579">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirtilerek hedef uç noktası oluşturmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-579">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="972af-580">Kullanırken `Url.Action` , ve için geçerli yol değerleri `controller` `action` çalışma zamanı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="972af-580">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="972af-581">`controller`Ve değeri `action` hem [ortam değerlerinin](#ambient) hem de değerlerinin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-581">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="972af-582">Yöntemi `Url.Action` her zaman ve geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-582">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="972af-583">Yönlendirme, bir URL oluştururken sağlanmayan bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="972af-583">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="972af-584">Ortam değerleriyle benzer bir yol düşünün `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="972af-584">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="972af-585">Yönlendirmenin ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="972af-585">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="972af-586">Tüm rota parametrelerinin bir değeri olduğundan, yönlendirmeye yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="972af-586">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="972af-587">Değer `{ d = Donovan }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="972af-587">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="972af-588">Değer `{ d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="972af-588">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="972af-589">Oluşturulan URL yolu `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="972af-589">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="972af-590">**Uyarı**: URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="972af-590">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="972af-591">Yukarıdaki örnekte, değer `{ c = Cheryl }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="972af-591">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="972af-592">Her iki değer de `{ c = Carol, d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="972af-592">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="972af-593">Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="972af-593">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="972af-594">İçin istenen değerleri `c` ve `d` bir URL oluşturmak için belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-594">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="972af-595">Varsayılan yol ile bu soruna isabet etmeniz gerekebilir `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="972af-595">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="972af-596">`Url.Action`Her zaman açıkça bir ve değeri belirttiğinden bu sorun pratikte nadir olarak belirlenir `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-596">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="972af-597">URL 'nin çeşitli aşırı yüklemeleri [. eylem](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) , ve dışındaki rota parametreleri için değerler sağlamak üzere bir yol değerleri nesnesi alır `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-597">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="972af-598">Route Values nesnesi genellikle ile birlikte kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="972af-598">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="972af-599">Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="972af-599">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="972af-600">Yol değerleri nesnesi:</span><span class="sxs-lookup"><span data-stu-id="972af-600">The route values object:</span></span>

* <span data-ttu-id="972af-601">Kural gereği genellikle anonim türdeki bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="972af-601">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="972af-602">Bir `IDictionary<>` veya [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).</span><span class="sxs-lookup"><span data-stu-id="972af-602">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="972af-603">Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.</span><span class="sxs-lookup"><span data-stu-id="972af-603">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="972af-604">Yukarıdaki kod oluşturulur `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="972af-604">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="972af-605">Aşağıdaki kod mutlak URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="972af-605">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="972af-606">Mutlak URL oluşturmak için, aşağıdakilerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="972af-606">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="972af-607">Kabul eden bir aşırı yükleme `protocol` .</span><span class="sxs-lookup"><span data-stu-id="972af-607">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="972af-608">Örneğin, yukarıdaki kod.</span><span class="sxs-lookup"><span data-stu-id="972af-608">For example, the preceding code.</span></span>
* <span data-ttu-id="972af-609">Varsayılan olarak mutlak URI 'Ler üreten [Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*).</span><span class="sxs-lookup"><span data-stu-id="972af-609">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="972af-610">Yola göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-610">Generate URLs by route</span></span>

<span data-ttu-id="972af-611">Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir.</span><span class="sxs-lookup"><span data-stu-id="972af-611">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="972af-612">`IUrlHelper`Ayrıca [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) ailesi yöntemlerin de sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-612">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="972af-613">Bu yöntemler [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)ile benzerdir, ancak geçerli değerlerini `action` ve `controller` Rota değerlerine kopyalamaz.</span><span class="sxs-lookup"><span data-stu-id="972af-613">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="972af-614">En yaygın kullanımı `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="972af-614">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="972af-615">URL oluşturmak için bir yol adı belirtir.</span><span class="sxs-lookup"><span data-stu-id="972af-615">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="972af-616">Genellikle bir denetleyici veya eylem adı belirtmez.</span><span class="sxs-lookup"><span data-stu-id="972af-616">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="972af-617">Aşağıdaki Razor Dosya, için BIR HTML bağlantısı oluşturur `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="972af-617">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="972af-618">HTML ve, URL 'Leri oluşturmaRazor</span><span class="sxs-lookup"><span data-stu-id="972af-618">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="972af-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>sırasıyla ve öğeleri oluşturmak Için [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini sağlar `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="972af-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="972af-620">Bu yöntemler bir URL oluşturmak için [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="972af-620">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="972af-621">`Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="972af-621">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="972af-622">Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-622">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="972af-623">Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-623">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="972af-624">Daha fazla bilgi için bkz. [Formlardaki etiket yardımcıları](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="972af-624">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="972af-625">Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-625">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="972af-626">Eylem sonuçlarında URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-626">URL generation in Action Results</span></span>

<span data-ttu-id="972af-627">Yukarıdaki örneklerde `IUrlHelper` bir denetleyicide kullanılması gösterildi.</span><span class="sxs-lookup"><span data-stu-id="972af-627">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="972af-628">Bir denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak bir URL oluşturmak olur.</span><span class="sxs-lookup"><span data-stu-id="972af-628">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="972af-629"><xref:Microsoft.AspNetCore.Mvc.ControllerBase>Ve <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-629">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="972af-630">Kullanıcı girişini kabul ettikten sonra, yaygın olarak kullanılan bir kullanım yeniden yönlendirilmelidir:</span><span class="sxs-lookup"><span data-stu-id="972af-630">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="972af-631">Eylem, gibi Fabrika yöntemleri sonuçları <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="972af-631">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="972af-632">Adanmış geleneksel yollar için özel durum</span><span class="sxs-lookup"><span data-stu-id="972af-632">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="972af-633">[Geleneksel yönlendirme](#cr) , [adanmış geleneksel yol](#dcr)olarak adlandırılan özel bir yol tanımı türünü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-633">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="972af-634">Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur:</span><span class="sxs-lookup"><span data-stu-id="972af-634">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="972af-635">Önceki yol tanımlarını kullanarak, `Url.Action("Index", "Home")` yolu kullanarak URL yolunu oluşturur `/` `default` , ancak neden?</span><span class="sxs-lookup"><span data-stu-id="972af-635">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="972af-636">Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="972af-636">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="972af-637">[Adanmış geleneksel yollar](#dcr) , karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır ve bu da yol, URL oluşturma ile çok fazla [dodan](xref:fundamentals/routing#greedy) yararlanın.</span><span class="sxs-lookup"><span data-stu-id="972af-637">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="972af-638">Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez.</span><span class="sxs-lookup"><span data-stu-id="972af-638">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="972af-639">Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-639">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="972af-640">`blog`Değerler EŞLEŞMEDIĞINDEN URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="972af-640">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="972af-641">Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="972af-641">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="972af-642">Alanlar</span><span class="sxs-lookup"><span data-stu-id="972af-642">Areas</span></span>

<span data-ttu-id="972af-643">[Bölgeler](xref:mvc/controllers/areas) , ilgili işlevselliği ayrı olarak bir grup içinde düzenlemek için kullanılan bir MVC özelliğidir:</span><span class="sxs-lookup"><span data-stu-id="972af-643">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="972af-644">Denetleyici eylemleri için yönlendirme ad alanı.</span><span class="sxs-lookup"><span data-stu-id="972af-644">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="972af-645">Görünümler için klasör yapısı.</span><span class="sxs-lookup"><span data-stu-id="972af-645">Folder structure for views.</span></span>

<span data-ttu-id="972af-646">Alanların kullanılması, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-646">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="972af-647">Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-647">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="972af-648">Bu bölümde yönlendirmenin alanlarla nasıl etkileşim kurduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="972af-648">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="972af-649">Alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-649">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="972af-650">Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve bir adlandırılmış yolu kullanacak şekilde yapılandırır `area` `area` `Blog` :</span><span class="sxs-lookup"><span data-stu-id="972af-650">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="972af-651">Önceki kodda, öğesini <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> oluşturmak için çağırılır `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="972af-651">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="972af-652">İkinci parametresi, `"Blog"` , alan adıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-652">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="972af-653">Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` yol, `"blog_route"` yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="972af-653">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="972af-654">`area`Rota değeri, için varsayılan bir değer tarafından üretilir `area` .</span><span class="sxs-lookup"><span data-stu-id="972af-654">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="972af-655">Tarafından oluşturulan yol `MapAreaControllerRoute` , aşağıdaki ile eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="972af-655">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="972af-656">`MapAreaControllerRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` .</span><span class="sxs-lookup"><span data-stu-id="972af-656">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="972af-657">Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="972af-657">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="972af-658">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-658">Conventional routing is order-dependent.</span></span> <span data-ttu-id="972af-659">Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-659">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="972af-660">Önceki örneği kullanarak, yol değerleri `{ area = Blog, controller = Users, action = AddUser }` aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="972af-660">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="972af-661">[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir alanın parçası olarak denetleyiciyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="972af-661">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="972af-662">Bu denetleyici `Blog` alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="972af-662">This controller is in the `Blog` area.</span></span> <span data-ttu-id="972af-663">Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir ve **not** `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="972af-663">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="972af-664">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="972af-664">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="972af-665">Her denetleyicinin ad alanı, tamamlanma açısından burada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="972af-665">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="972af-666">Yukarıdaki denetleyiciler aynı ad alanını kullanıyorsa, bir derleyici hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="972af-666">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="972af-667">Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="972af-667">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="972af-668">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` .</span><span class="sxs-lookup"><span data-stu-id="972af-668">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="972af-669">Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .</span><span class="sxs-lookup"><span data-stu-id="972af-669">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="972af-670">Değer *olmadan*eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-670">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="972af-671">Bir alan içinde bir eylem yürütürken, için rota değeri, `area` yönlendirme için, URL oluşturma için kullanılacak bir [çevresel değer](#ambient) olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-671">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="972af-672">Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-672">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="972af-673">Aşağıdaki kod, için bir URL oluşturur `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="972af-673">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="972af-674">Eylem tanımı</span><span class="sxs-lookup"><span data-stu-id="972af-674">Action definition</span></span>

<span data-ttu-id="972af-675">Bir denetleyicide, [Nonactıon](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özniteliğine sahip olanlar hariç genel yöntemler eylemlerdir.</span><span class="sxs-lookup"><span data-stu-id="972af-675">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="972af-676">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="972af-676">Sample code</span></span>

 * <span data-ttu-id="972af-677">[Mydisplayrouteınfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) yöntemi [örnek karşıdan yüklemeye](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) dahildir ve yönlendirme bilgilerini görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-677">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="972af-678">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="972af-678">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="972af-679">ASP.NET Core MVC, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-679">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="972af-680">Yollar başlangıç kodunda veya özniteliklerde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-680">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="972af-681">Yollar URL yollarının eylemlerle nasıl eşleştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="972af-681">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="972af-682">Yollar, yanıt olarak gönderilen URL 'Leri (bağlantılar için) oluşturmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-682">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="972af-683">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-683">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="972af-684">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-684">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="972af-685">Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="972af-685">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="972af-686">Bu belge, MVC ve yönlendirme arasındaki etkileşimleri ve tipik MVC uygulamalarının yönlendirme özelliklerini nasıl kullandığını açıklar.</span><span class="sxs-lookup"><span data-stu-id="972af-686">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="972af-687">Gelişmiş yönlendirme hakkında ayrıntılar için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-687">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="972af-688">Yönlendirme ara yazılımını ayarlama</span><span class="sxs-lookup"><span data-stu-id="972af-688">Setting up Routing Middleware</span></span>

<span data-ttu-id="972af-689">*Yapılandırma* yönteminde şuna benzer bir kod görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="972af-689">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="972af-690">Çağrısı içinde `UseMvc` , `MapRoute` yolu olarak başvurabileceğiniz tek bir yol oluşturmak için kullanılır `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-690">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="972af-691">Çoğu MVC uygulaması, yola benzer bir şablon içeren bir yol kullanır `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-691">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="972af-692">Yol şablonu, `"{controller=Home}/{action=Index}/{id?}"` gibi BIR URL yoluyla eşleşir `/Products/Details/5` ve `{ controller = Products, action = Details, id = 5 }` yolu simgeleştirileyerek yol değerlerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="972af-692">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="972af-693">MVC adlı bir denetleyiciyi bulmayı dener ve bu `ProductsController` eylemi çalıştırır `Details` :</span><span class="sxs-lookup"><span data-stu-id="972af-693">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="972af-694">Bu örnekte, model bağlamanın `id = 5` `id` Bu eylemi çağırırken parametresini olarak ayarlamak için değerini kullanacağı unutulmamalıdır `5` .</span><span class="sxs-lookup"><span data-stu-id="972af-694">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="972af-695">Daha fazla ayrıntı için [model bağlamaya](../models/model-binding.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-695">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="972af-696">Yolu kullanarak `default` :</span><span class="sxs-lookup"><span data-stu-id="972af-696">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="972af-697">Yol şablonu:</span><span class="sxs-lookup"><span data-stu-id="972af-697">The route template:</span></span>

* <span data-ttu-id="972af-698">`{controller=Home}``Home`Varsayılan olarak tanımlar`controller`</span><span class="sxs-lookup"><span data-stu-id="972af-698">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="972af-699">`{action=Index}``Index`Varsayılan olarak tanımlar`action`</span><span class="sxs-lookup"><span data-stu-id="972af-699">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="972af-700">`{id?}``id`isteğe bağlı olarak tanımlar</span><span class="sxs-lookup"><span data-stu-id="972af-700">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="972af-701">Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="972af-701">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="972af-702">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="972af-702">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="972af-703">`"{controller=Home}/{action=Index}/{id?}"`URL yoluyla eşleştirebilir `/` ve yol değerlerini oluşturacaktır `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="972af-703">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="972af-704">, `controller` `action` `id` URL yolunda karşılık gelen bir kesim olmadığından, için değerleri ve varsayılan değerleri kullanır, bir değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="972af-704">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="972af-705">MVC, ve eylemini seçmek için bu yol değerlerini `HomeController` kullanır `Index` :</span><span class="sxs-lookup"><span data-stu-id="972af-705">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="972af-706">Bu denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yollarından herhangi biri için yürütülür:</span><span class="sxs-lookup"><span data-stu-id="972af-706">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="972af-707">Kolaylık yöntemi `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="972af-707">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="972af-708">Değiştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="972af-708">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="972af-709">`UseMvc`ve `UseMvcWithDefaultRoute` `RouterMiddleware` , ara yazılım ardışık düzenine bir örneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="972af-709">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="972af-710">MVC, doğrudan ara yazılım ile etkileşime girmez ve istekleri işlemek için yönlendirmeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-710">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="972af-711">MVC, bir örneği aracılığıyla yollara bağlanır `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="972af-711">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="972af-712">İçindeki kod `UseMvc` Şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="972af-712">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="972af-713">`UseMvc`hiçbir yol doğrudan tanımlamaz, yol için yol koleksiyonuna bir yer tutucu ekler `attribute` .</span><span class="sxs-lookup"><span data-stu-id="972af-713">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="972af-714">Aşırı yükleme `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-714">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="972af-715">`UseMvc`ve tüm varyasyonları, yapılandırma şeklinden bağımsız olarak her zaman için öznitelik rotası özniteliği yönlendirme özelliği için bir yer tutucu ekler `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="972af-715">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="972af-716">`UseMvcWithDefaultRoute`Varsayılan bir yol tanımlar ve öznitelik yönlendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-716">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="972af-717">[Öznitelik yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-717">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="972af-718">Geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-718">Conventional routing</span></span>

<span data-ttu-id="972af-719">`default`Yol:</span><span class="sxs-lookup"><span data-stu-id="972af-719">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="972af-720">Yukarıdaki kod geleneksel yönlendirmeye bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="972af-720">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="972af-721">Bu stil, URL yolları için bir *kural* oluşturduğundan geleneksel yönlendirme olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="972af-721">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="972af-722">İlk yol segmenti, denetleyicinin adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-722">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="972af-723">İkincisi eylem adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-723">The second maps to the action name.</span></span>
* <span data-ttu-id="972af-724">Üçüncü segment, isteğe bağlı olarak kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="972af-724">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="972af-725">`id`bir model varlığına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-725">`id` maps to a model entity.</span></span>

<span data-ttu-id="972af-726">Bu `default` yolu kullanarak, URL yolu `/Products/List` eyleme eşlenir `ProductsController.List` ve `/Blog/Article/17` ile eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="972af-726">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="972af-727">Bu eşleme **yalnızca** denetleyiciye ve eylem adlarına dayalıdır ve ad alanları, kaynak dosya konumları veya yöntem parametrelerine göre değildir.</span><span class="sxs-lookup"><span data-stu-id="972af-727">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="972af-728">Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, tanımladığınız her eylem için yeni bir URL düzeniyle karşılaşmanıza gerek kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="972af-728">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="972af-729">CRUD stilinde eylemlere sahip bir uygulama için denetleyicilerinizdeki URL 'Lerin tutarlılığı, kodunuzun basitleştirilmesine ve Kullanıcı arabiriminizi daha öngörülebilir hale getirmenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-729">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="972af-730">, `id` Yol şablonu tarafından isteğe bağlı olarak tanımlanır ve bu, eylemlerinizin URL 'nin bir parçası olarak sağlanmadan yürütebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-730">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="972af-731">Genellikle `id` URL 'den atlanırsa ne olacağı, `0` model bağlama tarafından ayarlanabileceği ve sonuç olarak veritabanı eşleştirmesinin hiçbir varlık bulunamadığı durumlarda gerçekleşir `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="972af-731">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="972af-732">Öznitelik yönlendirme, bazı eylemler için gereken KIMLIĞI, diğerleri için değil, daha ayrıntılı bir denetim sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-732">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="972af-733">Kurala göre belgeler, `id` doğru kullanımda görünebilecekleri gibi isteğe bağlı parametreleri de içerecektir.</span><span class="sxs-lookup"><span data-stu-id="972af-733">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="972af-734">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="972af-734">Multiple routes</span></span>

<span data-ttu-id="972af-735">`UseMvc`Uygulamasına daha fazla çağrı ekleyerek içine birden çok yol ekleyebilirsiniz `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="972af-735">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="972af-736">Bunun yapılması, birden çok kural tanımlamanızı veya belirli bir eyleme adanmış geleneksel yollar eklemenizi sağlar; örneğin:</span><span class="sxs-lookup"><span data-stu-id="972af-736">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="972af-737">`blog`Burada yol, geleneksel bir *geleneksel yol*olduğundan geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme ayrılmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-737">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="972af-738">`controller` `action` Yol şablonunda parametre olarak görünmeyin, ancak bu yol yalnızca varsayılan değerlere sahip olabilir ve bu nedenle bu yol her zaman eyleme eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="972af-738">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="972af-739">Rota koleksiyonundaki yollar sıralanır ve eklendikleri sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-739">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="972af-740">Bu nedenle bu örnekte yol, `blog` yol öncesinde denenmeye sunulacaktır `default` .</span><span class="sxs-lookup"><span data-stu-id="972af-740">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-741">*Adanmış geleneksel yollar* genellıkle, URL yolunun kalan kısmını yakalamak için gibi **catch-all** yol parametrelerini kullanır `{*article}` .</span><span class="sxs-lookup"><span data-stu-id="972af-741">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="972af-742">Bu, ' çok Greedy ' yolunu diğer yollarla eşleştirirken hedeflediğiniz URL 'Lerle eşleşen bir yol haline getirir.</span><span class="sxs-lookup"><span data-stu-id="972af-742">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="972af-743">Bunu çözümlemek için ' Greedy ' yollarını daha sonra yol tablosuna koyun.</span><span class="sxs-lookup"><span data-stu-id="972af-743">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="972af-744">Geri dönüş</span><span class="sxs-lookup"><span data-stu-id="972af-744">Fallback</span></span>

<span data-ttu-id="972af-745">İstek işlemenin bir parçası olarak, MVC, uygulamanızdaki bir denetleyiciyi ve eylemi bulmak için yol değerlerinin kullanılabileceğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="972af-745">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="972af-746">Rota değerleri bir eylemle eşleşmezse, yol eşleşme olarak kabul edilmez ve sonraki rota denenir.</span><span class="sxs-lookup"><span data-stu-id="972af-746">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="972af-747">Buna *geri dönüş*denir ve geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="972af-747">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="972af-748">Kesinleştirme eylemleri</span><span class="sxs-lookup"><span data-stu-id="972af-748">Disambiguating actions</span></span>

<span data-ttu-id="972af-749">İki eylem yönlendirme aracılığıyla eşleşiyorsa, MVC ' en iyi ' adayı seçmek için bir özel durum oluşturması veya bir özel durum oluşturmak için, MVC 'nin belirsizliğini</span><span class="sxs-lookup"><span data-stu-id="972af-749">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="972af-750">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="972af-750">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="972af-751">Bu denetleyici, URL yolu ve rota verileri ile eşleşen iki eylemi tanımlar `/Products/Edit/17` `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="972af-751">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="972af-752">Bu, `Edit(int)` bir ürünü düzenlemek için bir form gösteren ve `Edit(int, Product)` postalanan formu işleyen MVC denetleyicileri için tipik bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="972af-752">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="972af-753">Bunu yapmak için `Edit(int, Product)` , istek BIR http olduğunda `POST` ve `Edit(int)` http fiili başka bir şey olduğunda bu olası MVC 'nin seçim yapması gerekir.</span><span class="sxs-lookup"><span data-stu-id="972af-753">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="972af-754">`HttpPostAttribute`( `[HttpPost]` ), `IActionConstraint` Yalnızca http fiili olduğunda eylemin seçili olmasını sağlayan bir uygulamasıdır `POST` .</span><span class="sxs-lookup"><span data-stu-id="972af-754">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="972af-755">' Nin varlığı `IActionConstraint` `Edit(int, Product)` ' daha iyi ' bir eşleşme yapar `Edit(int)` , `Edit(int, Product)` Bu nedenle ilk olarak denenir.</span><span class="sxs-lookup"><span data-stu-id="972af-755">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="972af-756">Yalnızca özelleştirilmiş senaryolarda özel uygulamalar yazmanız gerekir `IActionConstraint` , ancak benzer öznitelikler gibi özniteliklerin rolün `HttpPostAttribute` diğer HTTP fiilleri için tanımlandığını anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-756">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="972af-757">Geleneksel yönlendirmesinde, bir iş akışının parçası olduklarında aynı eylem adını kullanmak yaygın olarak karşılaşılan bir işlemdir `show form -> submit form` .</span><span class="sxs-lookup"><span data-stu-id="972af-757">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="972af-758">Bu düzenin rahatlığı, [ıactionconstraint 'ı anlama](#understanding-iactionconstraint) bölümünde daha sonra görünür hale gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-758">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="972af-759">Birden çok yol eşleşirse ve MVC ' en iyi ' yolu bulamazsa, bir oluşturur `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="972af-759">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="972af-760">Yol adları</span><span class="sxs-lookup"><span data-stu-id="972af-760">Route names</span></span>

<span data-ttu-id="972af-761">Dizeler `"blog"` ve `"default"` Aşağıdaki örneklerde yol adları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="972af-761">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="972af-762">Yol adları, URL oluşturma için adlandırılmış yolun kullanılabilmesi için yola mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="972af-762">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="972af-763">Bu, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı büyük ölçüde basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="972af-763">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="972af-764">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-764">Route names must be unique application-wide.</span></span>

<span data-ttu-id="972af-765">Yol adları, isteklerin URL 'SI ile eşleşmesini veya işlenmesini etkilemez; Bunlar yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-765">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="972af-766">[Yönlendirme](xref:fundamentals/routing) , MVC 'ye özgü yardımcılardaki URL oluşturma da dahil olmak üzere URL oluşturma hakkında daha ayrıntılı bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="972af-766">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="972af-767">Öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-767">Attribute routing</span></span>

<span data-ttu-id="972af-768">Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-768">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="972af-769">Aşağıdaki örnekte, `app.UseMvc();` `Configure` yönteminde kullanılır ve hiçbir yol geçirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-769">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="972af-770">`HomeController`Varsayılan yolun eşleşeceğini benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="972af-770">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="972af-771">`HomeController.Index()`Bu eylem, veya URL yollarının herhangi biri için yürütülür `/` `/Home` `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="972af-771">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-772">Bu örnek, öznitelik yönlendirme ve geleneksel yönlendirme arasında bir temel programlama farkı vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="972af-772">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="972af-773">Öznitelik yönlendirme, bir yol belirtmek için daha fazla giriş gerektirir; geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler.</span><span class="sxs-lookup"><span data-stu-id="972af-773">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="972af-774">Ancak, öznitelik yönlendirme (ve gerektirir) her eylem için hangi rota şablonlarının uygulanacağını kesin olarak denetler.</span><span class="sxs-lookup"><span data-stu-id="972af-774">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="972af-775">Öznitelik yönlendirme ile, denetleyici adı ve eylem adları, **herhangi** bir eylem seçildiği hiçbir rol oynar.</span><span class="sxs-lookup"><span data-stu-id="972af-775">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="972af-776">Bu örnek, önceki örnekle aynı URL 'Lerle eşleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="972af-776">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="972af-777">Yukarıdaki yol şablonları,, ve için yol parametreleri `action` tanımlamaz `area` `controller` .</span><span class="sxs-lookup"><span data-stu-id="972af-777">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="972af-778">Aslında, öznitelik rotalarında bu yol parametrelerine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="972af-778">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="972af-779">Yol şablonu bir eylemle zaten ilişkili olduğundan, URL 'den eylem adını ayrıştırmak mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="972af-779">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="972af-780">Http [fiil] öznitelikleriyle öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-780">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="972af-781">Öznitelik yönlendirme, gibi öznitelikleri de kullanabilir `Http[Verb]` `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="972af-781">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="972af-782">Bu özniteliklerin hepsi bir yol şablonunu kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-782">All of these attributes can accept a route template.</span></span> <span data-ttu-id="972af-783">Bu örnekte, aynı rota şablonuyla eşleşen iki eylem gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="972af-783">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="972af-784">Bir URL yolu için, `/products` `ProductsApi.ListProducts` http fiili olduğunda `GET` ve `ProductsApi.CreateProduct` http fiili olduğunda yürütülecektir, eylem `POST` gibi bir URL yolu yürütülür.</span><span class="sxs-lookup"><span data-stu-id="972af-784">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="972af-785">Öznitelik yönlendirme öncelikle URL ile yol öznitelikleri tarafından tanımlanan yol şablonları kümesine göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-785">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="972af-786">Bir rota şablonu eşleştiğinde, `IActionConstraint` hangi eylemlerin yürütüleceğini belirleyen kısıtlamalar uygulanır.</span><span class="sxs-lookup"><span data-stu-id="972af-786">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="972af-787">Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul edecek şekilde bir eylem yönteminde kullanmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-787">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="972af-788">`Http*Verb*Attributes`API 'nizin neleri desteklediği hakkında kesin olması için daha belirgin bir şekilde kullanılması daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="972af-788">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="972af-789">REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-789">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="972af-790">Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="972af-790">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="972af-791">Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.</span><span class="sxs-lookup"><span data-stu-id="972af-791">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="972af-792">Eylem, gibi bir URL yolu için `ProductsApi.GetProduct(int)` değil, gibi BIR URL yolu için yürütülür `/products/3` `/products` .</span><span class="sxs-lookup"><span data-stu-id="972af-792">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="972af-793">Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-793">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="972af-794">Yol adı</span><span class="sxs-lookup"><span data-stu-id="972af-794">Route Name</span></span>

<span data-ttu-id="972af-795">Aşağıdaki kod, bir *yol adı* tanımlar `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="972af-795">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="972af-796">Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-796">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="972af-797">Rota adlarının, yönlendirmenin URL eşleştirme davranışına etkisi yoktur ve yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-797">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="972af-798">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-798">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-799">Bunu, isteğe bağlı () parametresini tanımlayan geleneksel *varsayılan yol*ile kontrast `id` `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="972af-799">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="972af-800">API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .</span><span class="sxs-lookup"><span data-stu-id="972af-800">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="972af-801">Yolları birleştirme</span><span class="sxs-lookup"><span data-stu-id="972af-801">Combining routes</span></span>

<span data-ttu-id="972af-802">Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-802">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="972af-803">Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir.</span><span class="sxs-lookup"><span data-stu-id="972af-803">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="972af-804">Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-804">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="972af-805">Bu örnekte, URL yolu `/products` eşleştirebilir `ProductsApi.ListProducts` ve URL yolu `/products/5` eşleştirebilir `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="972af-805">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="972af-806">Bu eylemlerin her ikisi de `GET` ile işaretlendikleri IÇIN http ile eşleşir `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="972af-806">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="972af-807">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="972af-807">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="972af-808">Bu örnek, *varsayılan rotaya*benzer bir URL yolları kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-808">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="972af-809">Öznitelik yollarını sıralama</span><span class="sxs-lookup"><span data-stu-id="972af-809">Ordering attribute routes</span></span>

<span data-ttu-id="972af-810">Tanımlı bir düzende yürütülen geleneksel yolların aksine, öznitelik yönlendirme bir ağaç oluşturur ve tüm yollarla aynı anda eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-810">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="972af-811">Bu, yol girişleri ideal bir sıralamaya yerleştirildiyse olduğu gibi davranır; en özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.</span><span class="sxs-lookup"><span data-stu-id="972af-811">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="972af-812">Örneğin, gibi bir yol `blog/search/{topic}` gibi bir yol daha özeldir `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="972af-812">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="972af-813">`blog/search/{topic}`Tek yapmanız gereken tek bir sıralama olduğundan, varsayılan olarak ' çalıştırmaları ' yolunu mantıksal olarak konuşuyor.</span><span class="sxs-lookup"><span data-stu-id="972af-813">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="972af-814">Geleneksel yönlendirmeyi kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.</span><span class="sxs-lookup"><span data-stu-id="972af-814">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="972af-815">Öznitelik rotaları, `Order` Tüm Framework yol özniteliklerinin özelliğini kullanarak bir sıra yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-815">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="972af-816">Yollar, özelliğin artan sıralamasına göre işlenir `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-816">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="972af-817">Varsayılan sıra `0` .</span><span class="sxs-lookup"><span data-stu-id="972af-817">The default order is `0`.</span></span> <span data-ttu-id="972af-818">Kullanarak bir yolun ayarlanması `Order = -1` , bir sipariş ayarlamamadan önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="972af-818">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="972af-819">Kullanarak bir yolun ayarlanması `Order = 1` , varsayılan yol sıralaması sonrasında çalışır.</span><span class="sxs-lookup"><span data-stu-id="972af-819">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="972af-820">Uygulamasına bağlı kaçının `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-820">Avoid depending on `Order`.</span></span> <span data-ttu-id="972af-821">URL alanınız, doğru sıralama değerlerinin doğru şekilde yönlendirilmesini gerektiriyorsa, istemciler de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-821">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="972af-822">Genel öznitelik yönlendirme ' de, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="972af-822">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="972af-823">URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, bir geçersiz kılma olarak yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-823">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="972af-824">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="972af-824"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="972af-825">Sayfalar için yol Razor [ Razor ve uygulama kuralları: yol sırası](xref:razor-pages/razor-pages-conventions#route-order)bölümünde yer alan bilgi başlıkları hakkında bilgiler bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-825">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="972af-826">Yol şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])</span><span class="sxs-lookup"><span data-stu-id="972af-826">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="972af-827">Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (,) içine alarak *belirteç değiştirmeyi* destekler `[` `]` .</span><span class="sxs-lookup"><span data-stu-id="972af-827">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="972af-828">, Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="972af-828">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="972af-829">Aşağıdaki örnekte, Eylemler, açıklamalarda açıklandığı gibi URL yollarıyla eşleşir:</span><span class="sxs-lookup"><span data-stu-id="972af-829">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="972af-830">Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-830">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="972af-831">Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="972af-831">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="972af-832">Öznitelik rotaları de devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-832">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="972af-833">Bu özellikle, belirteç değiştirme ile güçlü bir şekilde birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-833">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="972af-834">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-834">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="972af-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir.</span><span class="sxs-lookup"><span data-stu-id="972af-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="972af-836">Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .</span><span class="sxs-lookup"><span data-stu-id="972af-836">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="972af-837">Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="972af-837">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="972af-838">Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-838">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="972af-839">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="972af-839">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="972af-840">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="972af-840">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="972af-841">, `RouteTokenTransformerConvention` Şu şekilde bir uygulama modeli kuralıdır:</span><span class="sxs-lookup"><span data-stu-id="972af-841">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="972af-842">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="972af-842">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="972af-843">Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="972af-843">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="972af-844">, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="972af-844">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="972af-845">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="972af-845">Multiple Routes</span></span>

<span data-ttu-id="972af-846">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-846">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="972af-847">Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi *varsayılan geleneksel yolun* davranışını taklit etmek olur:</span><span class="sxs-lookup"><span data-stu-id="972af-847">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="972af-848">Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biriyle birleşmesi anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-848">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="972af-849">Birden çok yol özniteliği (uygulayan `IActionConstraint` ) bir eyleme yerleştirildiğinde, her eylem kısıtlaması, onu tanımlayan öznitelikten yol şablonuyla birleştirir.</span><span class="sxs-lookup"><span data-stu-id="972af-849">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="972af-850">Eylemlerde birden çok yolun kullanılması güçlü görünse de, uygulamanızın URL alanının basit ve iyi tanımlanmış tutulması daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="972af-850">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="972af-851">Yalnızca gerektiğinde eylemler üzerinde birden çok yol kullanın, örneğin mevcut istemcileri desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="972af-851">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="972af-852">Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme</span><span class="sxs-lookup"><span data-stu-id="972af-852">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="972af-853">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="972af-853">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="972af-854">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="972af-854">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="972af-855">Kullanarak özel yol öznitelikleri`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="972af-855">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="972af-856">Çerçevede ( `[Route(...)]` ,, vb.) sunulan tüm rota öznitelikleri, `[HttpGet(...)]` `IRouteTemplateProvider` arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="972af-856">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="972af-857">MVC, uygulama başlatıldığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar ve `IRouteTemplateProvider` ilk rota kümesini oluşturmak için uygulamalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-857">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="972af-858">`IRouteTemplateProvider`Kendi yol öznitelerinizi tanımlamak için öğesini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-858">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="972af-859">Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="972af-859">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="972af-860">Yukarıdaki örnekteki özniteliği, `Template` `"api/[controller]"` ne zaman uygulanacağını otomatik olarak olarak ayarlar `[MyApiController]` .</span><span class="sxs-lookup"><span data-stu-id="972af-860">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="972af-861">Öznitelik yollarını özelleştirmek için uygulama modelini kullanma</span><span class="sxs-lookup"><span data-stu-id="972af-861">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="972af-862">*Uygulama modeli* , MVC tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta veriler ile başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-862">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="972af-863">*Uygulama modeli* , yol özniteliklerinden (aracılığıyla) toplanan tüm verileri içerir `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="972af-863">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="972af-864">Yönlendirme işleminin nasıl davranacağını özelleştirmek için, Başlangıç zamanında uygulama modelini değiştirmek üzere *kurallar* yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-864">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="972af-865">Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin basit bir örneği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="972af-865">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="972af-866">Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="972af-866">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="972af-867">MVC uygulamaları, geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-867">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="972af-868">Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.</span><span class="sxs-lookup"><span data-stu-id="972af-868">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="972af-869">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-869">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="972af-870">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-870">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="972af-871">Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="972af-871">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="972af-872">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemlerin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-872">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-873">İki tür yönlendirme sisteminin ayırt edilmesini ne kadar ayırt eden, bir URL bir yol şablonuyla eşleştirdikten sonra uygulanan işlemdir.</span><span class="sxs-lookup"><span data-stu-id="972af-873">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="972af-874">Geleneksel yönlendirmesinde, eşleşmeden yol değerleri, tüm geleneksel yönlendirilmiş eylemlerin arama tablosundan eylemi ve denetleyiciyi seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-874">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="972af-875">Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilendirilir ve başka bir arama gerekmez.</span><span class="sxs-lookup"><span data-stu-id="972af-875">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="972af-876">Karmaşık segmentler</span><span class="sxs-lookup"><span data-stu-id="972af-876">Complex segments</span></span>

<span data-ttu-id="972af-877">Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]` ), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir.</span><span class="sxs-lookup"><span data-stu-id="972af-877">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="972af-878">Bir açıklama için bkz. [kaynak kodu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="972af-878">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="972af-879">Daha fazla bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-879">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="972af-880">URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-880">URL Generation</span></span>

<span data-ttu-id="972af-881">MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-881">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="972af-882">URL oluşturma, kodlarınızın daha sağlam ve sürdürülebilir hale getirilmesi için sorunsuz kodlama URL 'Lerini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="972af-882">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="972af-883">Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="972af-883">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="972af-884">URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="972af-884">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="972af-885">`IUrlHelper`Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki temel altyapı parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-885">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="972af-886">`IUrlHelper` `Url` Denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir bir örnek bulacaksınız.</span><span class="sxs-lookup"><span data-stu-id="972af-886">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="972af-887">Bu örnekte `IUrlHelper` arabirim, `Controller.Url` başka bir eyleme YÖNELIK bir URL oluşturmak için özelliği aracılığıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-887">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="972af-888">Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkenin DEĞERI URL yol dizesi olacaktır `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="972af-888">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="972af-889">Bu URL yolu, yönlendirme değerlerini, geçerli istekten (çevresel değerler), `Url.Action` Bu değerleri geçirilen değerlerle ve bu değerlerin yol şablonuna geçirerek birleştirerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="972af-889">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="972af-890">Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="972af-890">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="972af-891">Bir değere sahip olmayan bir rota parametresi, varsa varsayılan bir değer kullanabilir veya isteğe bağlı ise ( `id` Bu örnekte olduğu gibi) atlanır.</span><span class="sxs-lookup"><span data-stu-id="972af-891">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="972af-892">Gerekli yol parametresinin karşılık gelen bir değeri yoksa, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="972af-892">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="972af-893">Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.</span><span class="sxs-lookup"><span data-stu-id="972af-893">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="972af-894">`Url.Action`Yukarıdaki örneği geleneksel yönlendirmeyi varsayar, ancak URL oluşturma özniteliği farklı olsa da, öznitelik yönlendirme ile benzer şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="972af-894">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="972af-895">Geleneksel yönlendirme ile, bir şablonu genişletmek için yol değerleri kullanılır ve `controller` `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme Ile eşleşen URL 'ler bir *kurala*bağlı olduğundan, bu işe yarar.</span><span class="sxs-lookup"><span data-stu-id="972af-895">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="972af-896">Öznitelik yönlendirmesinde, ve için rota değerlerinin `controller` `action` şablonda görünmesine izin verilmez; bunun yerine kullanılacak şablonu aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-896">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="972af-897">Bu örnek öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="972af-897">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="972af-898">MVC, tüm öznitelik yönlendirilmiş eylemlerinin bir arama tablosunu oluşturur ve `controller` `action` URL oluşturma için kullanılacak yol şablonunu seçmek için ve değerleriyle eşleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="972af-898">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="972af-899">Yukarıdaki örnekte `custom/url/to/destination` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="972af-899">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="972af-900">Eylem adına göre URL 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-900">Generating URLs by action name</span></span>

<span data-ttu-id="972af-901">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="972af-901">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="972af-902">`Action`) ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirterek ne bağlandığınızı belirtmek istediğiniz fikri temel alır.</span><span class="sxs-lookup"><span data-stu-id="972af-902">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-903">Kullanırken `Url.Action` , için geçerli yol değerleri `controller` ve `action` değeri için belirtilir `controller` ve `action` her iki *çevresel değerin* **ve** *değerin*bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-903">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="972af-904">Yöntemi `Url.Action` her zaman ve ' nin geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="972af-904">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="972af-905">Yönlendirme, bir URL oluştururken sağlamadığınız bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmayı dener.</span><span class="sxs-lookup"><span data-stu-id="972af-905">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="972af-906">`{a}/{b}/{c}/{d}`Yönlendirme ve ortam değerleri gibi bir yol kullanarak `{ a = Alice, b = Bob, c = Carol, d = David }` , yönlendirmenin tüm rota parametreleri bir değere sahip olduğundan, ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="972af-906">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="972af-907">Değeri eklediyseniz `{ d = Donovan }` değer `{ d = David }` yok sayılır ve oluşturulan URL yolu da olur `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="972af-907">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="972af-908">URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="972af-908">URL paths are hierarchical.</span></span> <span data-ttu-id="972af-909">Yukarıdaki örnekte, değerini eklediyseniz, `{ c = Cheryl }` her iki değer de `{ c = Carol, d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="972af-909">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="972af-910">Bu durumda artık için bir değer yoktur `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="972af-910">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="972af-911">İstenen değerini `c` ve ' i belirtmeniz gerekir `d` .</span><span class="sxs-lookup"><span data-stu-id="972af-911">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="972af-912">Varsayılan yol () ile bu soruna yaklaşmanız gerekebilir, `{controller}/{action}/{id?}` ancak `Url.Action` her zaman açıkça bir ve değeri belirtmek için uygulamada bu davranış hakkında nadiren karşılaşacaksınız `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-912">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="972af-913">Daha uzun aşırı yüklemeleri `Url.Action` Ayrıca, ve dışındaki rota parametreleri için değerler sağlamak üzere ek bir *yol değerleri* nesnesi de alır `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-913">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="972af-914">Bunun en yaygın olarak bu şekilde kullanıldığını görürsünüz `id` `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="972af-914">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="972af-915">Kurala göre *yol değerleri* nesnesi genellikle anonim türdeki bir nesnedir, ancak bir `IDictionary<>` veya *düz olarak eski .net nesnesi*de olabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-915">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="972af-916">Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.</span><span class="sxs-lookup"><span data-stu-id="972af-916">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="972af-917">Mutlak URL oluşturmak için şunu kabul eden bir aşırı yükleme kullanın `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="972af-917">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="972af-918">Rotaya göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-918">Generating URLs by route</span></span>

<span data-ttu-id="972af-919">Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir.</span><span class="sxs-lookup"><span data-stu-id="972af-919">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="972af-920">`IUrlHelper`Ayrıca `Url.RouteUrl` Yöntem ailesini de sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-920">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="972af-921">Bu yöntemler öğesine benzerdir `Url.Action` , ancak geçerli değerlerini `action` ve yol değerlerini kopyalamalardır `controller` .</span><span class="sxs-lookup"><span data-stu-id="972af-921">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="972af-922">En yaygın kullanım, genellikle bir denetleyici veya eylem *adı belirtmeden,* URL oluşturmak için belirli bir yolu kullanmak üzere bir yol adı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="972af-922">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="972af-923">HTML 'de URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-923">Generating URLs in HTML</span></span>

<span data-ttu-id="972af-924">`IHtmlHelper`, ve `HtmlHelper` `Html.BeginForm` `Html.ActionLink` öğelerini sırasıyla oluşturmak ve oluşturmak için yöntemler sağlar `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="972af-924">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="972af-925">Bu yöntemler `Url.Action` BIR URL oluşturmak için yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="972af-925">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="972af-926">`Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="972af-926">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="972af-927">Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-927">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="972af-928">Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="972af-928">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="972af-929">Daha fazla bilgi için bkz. [formlarla çalışma](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="972af-929">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="972af-930">Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-930">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="972af-931">Eylem sonuçlarında URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="972af-931">Generating URLS in Action Results</span></span>

<span data-ttu-id="972af-932">Yukarıdaki örnekler `IUrlHelper` bir denetleyicide kullanılarak gösteriliyor, ancak denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak BIR URL oluşturmak olur.</span><span class="sxs-lookup"><span data-stu-id="972af-932">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="972af-933">`ControllerBase`Ve `Controller` temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-933">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="972af-934">Tipik bir kullanım, Kullanıcı girişi kabul edildikten sonra yeniden yönlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-934">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="972af-935">Eylem sonuçları Fabrika yöntemleri, üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="972af-935">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="972af-936">Adanmış geleneksel yollar için özel durum</span><span class="sxs-lookup"><span data-stu-id="972af-936">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="972af-937">Geleneksel yönlendirme, *adanmış geleneksel yol*olarak adlandırılan özel bir yol tanımı türünü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-937">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="972af-938">Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur.</span><span class="sxs-lookup"><span data-stu-id="972af-938">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="972af-939">Bu yol tanımlarının kullanılması, `Url.Action("Index", "Home")` yol Ile URL yolunu oluşturacak `/` `default` , ancak neden?</span><span class="sxs-lookup"><span data-stu-id="972af-939">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="972af-940">Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="972af-940">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="972af-941">Adanmış geleneksel yollar, URL oluşturmayla "çok Greedy" olmasını önleyen karşılık gelen bir yol parametresi olmayan varsayılan değerlerin özel bir davranışına bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-941">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="972af-942">Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez.</span><span class="sxs-lookup"><span data-stu-id="972af-942">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="972af-943">Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-943">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="972af-944">`blog`Değerler eşleşmediğinden, KULLANıLARAK URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="972af-944">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="972af-945">Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="972af-945">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="972af-946">Alanlar</span><span class="sxs-lookup"><span data-stu-id="972af-946">Areas</span></span>

<span data-ttu-id="972af-947">[Bölgeler](areas.md) , ilgili işlevselliği ayrı bir yönlendirme-ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir grupla düzenlemek için kullanılan bir MVC özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="972af-947">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="972af-948">Alanların kullanılması, bir uygulamanın farklı *alanlara*sahip oldukları sürece aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="972af-948">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="972af-949">Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="972af-949">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="972af-950">Bu bölüm, yönlendirmenin alanlarla nasıl etkileşime gireceğini tartışır. alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](areas.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="972af-950">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="972af-951">Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve adlı bir alan için bir *alan yolu* kullanacak şekilde yapılandırır `Blog` :</span><span class="sxs-lookup"><span data-stu-id="972af-951">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="972af-952">Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` , ilk yol yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="972af-952">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="972af-953">`area`Yol değeri, `area` tarafından oluşturulan yolun aşağıdaki değere eşit olduğu aslında için varsayılan bir değer tarafından üretilir `MapAreaRoute` :</span><span class="sxs-lookup"><span data-stu-id="972af-953">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="972af-954">`MapAreaRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` .</span><span class="sxs-lookup"><span data-stu-id="972af-954">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="972af-955">Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="972af-955">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="972af-956">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-956">Conventional routing is order-dependent.</span></span> <span data-ttu-id="972af-957">Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-957">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="972af-958">Yukarıdaki örneği kullanarak, yol değerleri aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="972af-958">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="972af-959">, Bir `AreaAttribute` alanın parçası olarak denetleyiciyi belirtir, bu denetleyicinin alanında olduğunu varsayalım `Blog` .</span><span class="sxs-lookup"><span data-stu-id="972af-959">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="972af-960">Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir **not** ve `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="972af-960">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="972af-961">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="972af-961">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="972af-962">Her denetleyicinin ad alanı, tamamlanma için burada gösterilir. Aksi takdirde, denetleyicilerde adlandırma çakışması olur ve derleyici hatası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="972af-962">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="972af-963">Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="972af-963">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="972af-964">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` .</span><span class="sxs-lookup"><span data-stu-id="972af-964">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="972af-965">Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .</span><span class="sxs-lookup"><span data-stu-id="972af-965">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="972af-966">Değer *olmadan*eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="972af-966">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="972af-967">Bir alan içinde bir eylem yürütürken, için rota değeri, `area` URL oluşturma için kullanılmak üzere yönlendirme için bir *ortam değeri* olarak kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="972af-967">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="972af-968">Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="972af-968">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="972af-969">Iactionconstraint 'i anlama</span><span class="sxs-lookup"><span data-stu-id="972af-969">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="972af-970">Bu bölüm, Framework iç işlevleri hakkında ayrıntılı bir bakış ve MVC 'nin yürütülecek eylemi nasıl seçtiği.</span><span class="sxs-lookup"><span data-stu-id="972af-970">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="972af-971">Tipik bir uygulamanın özel olması gerekmez`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="972af-971">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="972af-972">Arabirime tanıdık olmasanız bile büyük olasılıkla zaten kullandık `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="972af-972">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="972af-973">`[HttpGet]`Özniteliği ve benzer `[Http-VERB]` öznitelikleri, `IActionConstraint` bir eylem yönteminin yürütülmesini sınırlandırmak için uygular.</span><span class="sxs-lookup"><span data-stu-id="972af-973">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="972af-974">Varsayılan geleneksel yol varsayılıyor, URL yolu `/Products/Edit` `{ controller = Products, action = Edit }` burada gösterilen eylemlerin **her ikisiyle de** eşleşen değerleri üretir.</span><span class="sxs-lookup"><span data-stu-id="972af-974">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="972af-975">`IActionConstraint`Terminolojisinde, her ikisi de rota verileriyle eşleşen her iki eylemin da aday kabul edileceğini söyliyoruz.</span><span class="sxs-lookup"><span data-stu-id="972af-975">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="972af-976">`HttpGetAttribute`Yürütüldüğünde, *Düzenle ()* , *Get* için BIR eşleşmedir ve diğer http fiili için bir eşleşme değildir.</span><span class="sxs-lookup"><span data-stu-id="972af-976">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="972af-977">`Edit(...)`Eylemin tanımlı hiçbir kısıtlaması yok, bu nedenle herhangi BIR http fiili ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="972af-977">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="972af-978">Bu nedenle, yalnızca bir eşleşme olduğunu varsayıyoruz `POST` `Edit(...)` .</span><span class="sxs-lookup"><span data-stu-id="972af-978">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="972af-979">Ancak, `GET` her iki eylem de eşleşemez, ancak bir eylem, olmadan bir eylemden `IActionConstraint` her zaman *daha iyi* kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="972af-979">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="972af-980">Bu nedenle `Edit()` `[HttpGet]` , daha belirli bir kabul edildiğinden ve her iki eylem de eşleşeceğinden seçilecek.</span><span class="sxs-lookup"><span data-stu-id="972af-980">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="972af-981">Kavramsal olarak, `IActionConstraint` bir *aşırı yükleme*biçimidir, ancak aynı ada sahip yöntemlerin aşırı YÜKLENMESI yerine aynı URL ile eşleşen eylemler arasında aşırı yükleme yapılır.</span><span class="sxs-lookup"><span data-stu-id="972af-981">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="972af-982">Öznitelik yönlendirme Ayrıca, `IActionConstraint` farklı denetleyicilerle her ikisi de olarak kabul edilen eylemlere neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="972af-982">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="972af-983">Iactionconstraint uygulama</span><span class="sxs-lookup"><span data-stu-id="972af-983">Implementing IActionConstraint</span></span>

<span data-ttu-id="972af-984">' Nin uygulanmasının en kolay yolu, `IActionConstraint` ' dan türetilmiş bir sınıf oluşturmaktır `System.Attribute` ve bunları eylemleriniz ve denetleyicilerinize yerleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="972af-984">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="972af-985">MVC, öznitelik olarak uygulanan her türlü otomatik olarak keşfedilir `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="972af-985">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="972af-986">Kısıtlama uygulamak için uygulama modelini kullanabilirsiniz ve bu, büyük olasılıkla en esnek yaklaşımdır ve bu sayede, nasıl uygulanabileceğini meta programlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-986">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="972af-987">Aşağıdaki örnekte, bir kısıtlama yol verilerinden bir *ülke kodunu* temel alan bir eylem seçer.</span><span class="sxs-lookup"><span data-stu-id="972af-987">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="972af-988">[GitHub 'daki tam örnek](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="972af-988">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="972af-989">Yöntemi uygulamaktan `Accept` ve kısıtlamanın yürütülmesi için bir ' Order ' seçmeye sorumlusunuz.</span><span class="sxs-lookup"><span data-stu-id="972af-989">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="972af-990">Bu durumda, yöntemi, `Accept` `true` yol değeri eşleştiğinde eylemin bir eşleşme olduğunu belirtmek için öğesini döndürür `country` .</span><span class="sxs-lookup"><span data-stu-id="972af-990">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="972af-991">Bu, öğesinden farklı bir `RouteValueAttribute` eyleme geri dönüş yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="972af-991">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="972af-992">Örnek, bir eylem tanımlarsanız, `en-US` gibi bir ülke kodunun `fr-FR` , uygulanmamış daha fazla genel denetleyiciye geri dönemeyeceğini gösterir `[CountrySpecific(...)]` .</span><span class="sxs-lookup"><span data-stu-id="972af-992">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="972af-993">`Order`Özelliği kısıtlamanın parçası olan *aşamayı* belirler.</span><span class="sxs-lookup"><span data-stu-id="972af-993">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="972af-994">Eylem kısıtlamaları, gruplarına göre gruplar içinde çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="972af-994">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="972af-995">Örneğin, tüm Framework tarafından sunulan HTTP yöntemi öznitelikleri aynı `Order` aşamada çalışacak şekilde aynı değeri kullanır.</span><span class="sxs-lookup"><span data-stu-id="972af-995">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="972af-996">İstediğiniz ilkeleri uygulamak için ihtiyacınız olan çok sayıda aşamaya sahip olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="972af-996">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="972af-997">Bir değere karar vermek için, `Order` kısıtlamalarınızın http yöntemlerinden önce uygulanıp uygulanmayacağı hakkında düşünün.</span><span class="sxs-lookup"><span data-stu-id="972af-997">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="972af-998">Daha az sayı önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="972af-998">Lower numbers run first.</span></span>

::: moniker-end
