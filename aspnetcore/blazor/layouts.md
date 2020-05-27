---
<span data-ttu-id="68534-101">Başlık: ' ASP.NET Core Blazor düzenleri ' Yazar: Açıklama: ' uygulamalar için yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="68534-101">title: 'ASP.NET Core Blazor layouts' author: description: 'Learn how to create reusable layout components for Blazor apps.'</span></span>
<span data-ttu-id="68534-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68534-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68534-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68534-103">'Blazor'</span></span>
- <span data-ttu-id="68534-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68534-104">'Identity'</span></span>
- <span data-ttu-id="68534-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68534-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="68534-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68534-106">'Razor'</span></span>
- <span data-ttu-id="68534-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68534-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="68534-108">ASP.NET Core Blazor düzenleri</span><span class="sxs-lookup"><span data-stu-id="68534-108">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="68534-109">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68534-109">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="68534-110">Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="68534-110">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="68534-111">Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak etkili bir yaklaşım değildir.</span><span class="sxs-lookup"><span data-stu-id="68534-111">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="68534-112">Öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="68534-112">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="68534-113">Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="68534-113">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="68534-114">*Düzenler* bu sorunu çözüyor.</span><span class="sxs-lookup"><span data-stu-id="68534-114">*Layouts* solve this problem.</span></span>

<span data-ttu-id="68534-115">Teknik olarak, düzen yalnızca başka bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="68534-115">Technically, a layout is just another component.</span></span> <span data-ttu-id="68534-116">Düzen bir Razor şablonda veya C# kodunda tanımlanır ve [veri bağlama](xref:blazor/data-binding), [bağımlılık ekleme](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="68534-116">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="68534-117">Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:</span><span class="sxs-lookup"><span data-stu-id="68534-117">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="68534-118"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase> <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Düzen içindeki işlenmiş içerik için bir özellik tanımlayan öğesinden devralır.</span><span class="sxs-lookup"><span data-stu-id="68534-118">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="68534-119">Razor `@Body` İçeriğin işlendiği yerleşim biçimlendirmesinde konumu belirtmek için söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="68534-119">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="68534-120">Aşağıdaki kod örneğinde Razor , *mainlayout. Razor*olan bir düzen bileşeninin şablonu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="68534-120">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="68534-121">Düzen, <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> `@Body` Gezinti çubuğu ve alt bilgi arasında devralır ve Ayarlar:</span><span class="sxs-lookup"><span data-stu-id="68534-121">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="68534-122">Uygulama şablonlarından birini temel alan bir uygulamada Blazor , `MainLayout` bileşen (*mainlayout. Razor*) uygulamanın *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="68534-122">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="68534-123">Varsayılan düzen</span><span class="sxs-lookup"><span data-stu-id="68534-123">Default layout</span></span>

<span data-ttu-id="68534-124"><xref:Microsoft.AspNetCore.Components.Routing.Router>Uygulamanın App *. Razor* dosyasındaki bileşende varsayılan uygulama yerleşimini belirtin.</span><span class="sxs-lookup"><span data-stu-id="68534-124">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's *App.razor* file.</span></span> <span data-ttu-id="68534-125"><xref:Microsoft.AspNetCore.Components.Routing.Router>Varsayılan Şablonlar tarafından belirtilen aşağıdaki bileşen, Blazor bileşene varsayılan düzeni ayarlar `MainLayout` :</span><span class="sxs-lookup"><span data-stu-id="68534-125">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="68534-126">İçerik için varsayılan bir düzen sağlamak üzere <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bir içerik belirtin <xref:Microsoft.AspNetCore.Components.LayoutView> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> :</span><span class="sxs-lookup"><span data-stu-id="68534-126">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="68534-127">Bileşen hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.Routing.Router> bkz <xref:blazor/routing> ..</span><span class="sxs-lookup"><span data-stu-id="68534-127">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="68534-128">Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="68534-128">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="68534-129">En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="68534-129">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="68534-130">Bir bileşende düzen belirtme</span><span class="sxs-lookup"><span data-stu-id="68534-130">Specify a layout in a component</span></span>

<span data-ttu-id="68534-131">Razor `@layout` Bir bileşene düzen uygulamak için yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="68534-131">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="68534-132">Derleyici, `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> bileşen sınıfına uygulanan öğesine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="68534-132">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="68534-133">Aşağıdaki `MasterList` bileşenin içeriği konumuna öğesine eklenir `MasterLayout` `@Body` :</span><span class="sxs-lookup"><span data-stu-id="68534-133">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="68534-134">Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendiricide ayarlanan *varsayılan bir düzen* veya `@layout` *_Imports. Razor*'den içeri aktarılan bir yönerge geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="68534-134">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="68534-135">Merkezi düzen seçimi</span><span class="sxs-lookup"><span data-stu-id="68534-135">Centralized layout selection</span></span>

<span data-ttu-id="68534-136">Bir uygulamanın her klasörü, isteğe bağlı olarak *_Imports. Razor*adlı bir şablon dosyası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="68534-136">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="68534-137">Derleyici, içeri aktarmalar dosyasında belirtilen yönergeleri Razor aynı klasörde ve özyinelemeli olarak tüm alt klasörlerinde bulunan tüm şablonlarda içerir.</span><span class="sxs-lookup"><span data-stu-id="68534-137">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="68534-138">Bu nedenle, içeren bir *_Imports. Razor* dosyası, `@layout MyCoolLayout` bir klasördeki tüm bileşenlerin kullanımını sağlar `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="68534-138">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="68534-139">`@layout MyCoolLayout`Klasör ve alt klasörlerdeki tüm *. Razor* dosyalarına tekrar tekrar ekleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="68534-139">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="68534-140">`@using`yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="68534-140">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="68534-141">Aşağıdaki *_Imports. Razor* dosyası içeri aktarmaları:</span><span class="sxs-lookup"><span data-stu-id="68534-141">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="68534-142">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="68534-142">`MyCoolLayout`.</span></span>
* <span data-ttu-id="68534-143">RazorAynı klasörde ve tüm alt klasörlerde bulunan tüm bileşenler.</span><span class="sxs-lookup"><span data-stu-id="68534-143">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="68534-144">`BlazorApp1.Data`Ad alanı.</span><span class="sxs-lookup"><span data-stu-id="68534-144">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="68534-145">*_Imports. Razor* dosyası, [ Razor görünümler ve sayfalar için _ViewImports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzerdir, ancak özellikle Razor bileşen dosyalarına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="68534-145">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="68534-146">_Imports bir düzen belirtme *. Razor* , yönlendiricinin *varsayılan düzeni*olarak belirtilen bir düzeni geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="68534-146">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="68534-147">İç içe düzenleri</span><span class="sxs-lookup"><span data-stu-id="68534-147">Nested layouts</span></span>

<span data-ttu-id="68534-148">Uygulamalar iç içe düzenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="68534-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="68534-149">Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="68534-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="68534-150">Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="68534-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="68534-151">Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="68534-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="68534-152">*Epısodescomponent. Razor* dosyası görüntülenecek bileşendir.</span><span class="sxs-lookup"><span data-stu-id="68534-152">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="68534-153">Bileşen öğesine başvurur `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="68534-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="68534-154">*Masterlistlayout. Razor* dosyası sağlar `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="68534-154">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="68534-155">Düzen, nerede işlendiği başka bir düzene başvurur `MasterLayout` .</span><span class="sxs-lookup"><span data-stu-id="68534-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="68534-156">`EpisodesComponent`, görüntülendiği yerde işlenir `@Body` :</span><span class="sxs-lookup"><span data-stu-id="68534-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="68534-157">Son olarak `MasterLayout` , *masterlayout. Razor* içinde üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir.</span><span class="sxs-lookup"><span data-stu-id="68534-157">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="68534-158">`MasterListLayout`ile birlikte `EpisodesComponent` işlendiğinde, şu şekilde `@Body` görünür:</span><span class="sxs-lookup"><span data-stu-id="68534-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="68534-159">RazorTümleşik bileşenlerle bir sayfa düzeni paylaşma</span><span class="sxs-lookup"><span data-stu-id="68534-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="68534-160">Yönlendirilebilir bileşenler bir sayfalar uygulamasıyla tümleştirildiğinde Razor , uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="68534-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="68534-161">Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="68534-161">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68534-162">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="68534-162">Additional resources</span></span>

* <xref:mvc/views/layout>
