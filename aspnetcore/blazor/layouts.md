---
title: ASP.NET Blazor Çekirdek düzenleri
author: guardrex
description: Uygulamalar için Blazor yeniden kullanılabilir düzen bileşenlerini nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660414"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="456a8-103">ASP.NET Blazor Çekirdek düzenleri</span><span class="sxs-lookup"><span data-stu-id="456a8-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="456a8-104">Yazar: [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="456a8-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="456a8-105">Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin bir parçasıdır ve uygulamadaki her bileşen tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="456a8-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="456a8-106">Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak,&mdash;öğelerden biri güncelleştirme gerektirdiğinde, her bileşengüncelleştirilse etkili bir yaklaşım değildir.</span><span class="sxs-lookup"><span data-stu-id="456a8-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="456a8-107">Bu tür yinelemekorumak zordur ve zaman içinde tutarsız içeriğe yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="456a8-108">*Düzenlerbu* sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="456a8-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="456a8-109">Teknik olarak, bir düzen sadece başka bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="456a8-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="456a8-110">Bir düzen, Razor şablonunda veya C# kodunda tanımlanır ve [veri bağlama,](xref:blazor/data-binding) [bağımlılık enjeksiyonu](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="456a8-111">Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:</span><span class="sxs-lookup"><span data-stu-id="456a8-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="456a8-112">Düzen içinde `LayoutComponentBase`işlenen içerik `Body` için bir özellik tanımlayan, devralır.</span><span class="sxs-lookup"><span data-stu-id="456a8-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="456a8-113">İçeriğin işlendiği `@Body` düzen biçimlendirmesinde konumu belirtmek için Razor sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="456a8-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="456a8-114">Aşağıdaki kod örneği, bir düzen bileşeni olan *MainLayout.razor'un*Razor şablonunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="456a8-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="456a8-115">Düzen, gezinti `LayoutComponentBase` çubuğu `@Body` ile altbilgi arasındaki devralır ve ayarlar:</span><span class="sxs-lookup"><span data-stu-id="456a8-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="456a8-116">Blazor Uygulama şablonlarından birine dayalı bir uygulamada, `MainLayout` bileşen *(MainLayout.razor)* uygulamanın *Paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="456a8-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="456a8-117">Varsayılan düzen</span><span class="sxs-lookup"><span data-stu-id="456a8-117">Default layout</span></span>

<span data-ttu-id="456a8-118">Uygulamanın *App.razor* `Router` dosyasındaki bileşendeki varsayılan uygulama düzenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="456a8-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="456a8-119">Varsayılan `Router` Blazor şablonlar tarafından sağlanan aşağıdaki bileşen, varsayılan düzeni `MainLayout` bileşene ayarlar:</span><span class="sxs-lookup"><span data-stu-id="456a8-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="456a8-120">İçerik için varsayılan `NotFound` bir düzen `LayoutView` sağlamak `NotFound` için, bir içerik için belirtin:</span><span class="sxs-lookup"><span data-stu-id="456a8-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="456a8-121">`Router` Bileşen hakkında daha fazla <xref:blazor/routing>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="456a8-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="456a8-122">Düzenin yönlendiricide varsayılan düzen olarak belirtilmesi yararlı bir uygulamadır, çünkü bileşen başına veya klasör başına olarak geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="456a8-123">En genel teknik olduğundan uygulamanın varsayılan düzenini ayarlamak için yönlendiriciyi kullanmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="456a8-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="456a8-124">Bileşende düzen belirtin</span><span class="sxs-lookup"><span data-stu-id="456a8-124">Specify a layout in a component</span></span>

<span data-ttu-id="456a8-125">Bir bileşene `@layout` düzen uygulamak için Razor yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="456a8-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="456a8-126">Derleyici, bileşen `@layout` sınıfına uygulanan bir `LayoutAttribute`, a dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="456a8-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="456a8-127">Aşağıdaki `MasterList` bileşenin içeriği aşağıdaki konuma `MasterLayout` `@Body`eklenir:</span><span class="sxs-lookup"><span data-stu-id="456a8-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="456a8-128">Bir bileşende düzeni doğrudan belirtmek, yönlendiricide ayarlanan *varsayılan* `@layout` düzeni veya *_Imports.razor'dan*alınan bir yönergeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="456a8-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="456a8-129">Merkezi düzen seçimi</span><span class="sxs-lookup"><span data-stu-id="456a8-129">Centralized layout selection</span></span>

<span data-ttu-id="456a8-130">Bir uygulamanın her klasörü isteğe bağlı olarak *_Imports.razor*adlı bir şablon dosyası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="456a8-131">Derleyici, tüm razor şablonlarında, tüm alt klasörlerinde ve özyinelemeli olarak içe aktarım dosyasında belirtilen yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="456a8-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="456a8-132">Bu nedenle, *_Imports.razor* dosyası içeren `@layout MyCoolLayout` bir klasördeki tüm `MyCoolLayout`bileşenlerin kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="456a8-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="456a8-133">Klasör ve alt klasörlerdeki `@layout MyCoolLayout` tüm *.razor* dosyalarına tekrar tekrar eklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="456a8-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="456a8-134">`@using`direktifler de bileşenlere aynı şekilde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="456a8-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="456a8-135">Aşağıdaki *_Imports.razor* dosya alma:</span><span class="sxs-lookup"><span data-stu-id="456a8-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="456a8-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="456a8-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="456a8-137">Aynı klasördeki tüm Razor bileşenleri ve alt klasörler.</span><span class="sxs-lookup"><span data-stu-id="456a8-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="456a8-138">İsim `BlazorApp1.Data` alanı.</span><span class="sxs-lookup"><span data-stu-id="456a8-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="456a8-139">*_Imports.razor* [dosyası, Razor görünümleri ve sayfaları için _ViewImports.cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzer, ancak özellikle Razor bileşen dosyalarına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="456a8-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="456a8-140">*_Imports.razor'da* bir düzen belirtilmesi, yönlendiricinin varsayılan *düzeni*olarak belirtilen düzeni geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="456a8-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="456a8-141">İç içe düzenler</span><span class="sxs-lookup"><span data-stu-id="456a8-141">Nested layouts</span></span>

<span data-ttu-id="456a8-142">Uygulamalar iç içe doğru düzenlerden oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="456a8-143">Bileşen, sırayla başka bir düzene başvuran bir düzene başvurur.</span><span class="sxs-lookup"><span data-stu-id="456a8-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="456a8-144">Örneğin, iç içe geçme düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="456a8-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="456a8-145">Aşağıdaki örnekte iç içe kullanılan düzenlerin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="456a8-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="456a8-146">*EpisodesComponent.razor* dosyası görüntülenecek bileşendir.</span><span class="sxs-lookup"><span data-stu-id="456a8-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="456a8-147">Bileşen `MasterListLayout`başvurur:</span><span class="sxs-lookup"><span data-stu-id="456a8-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="456a8-148">*MasterListLayout.razor* dosyası `MasterListLayout`sağlar.</span><span class="sxs-lookup"><span data-stu-id="456a8-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="456a8-149">Düzen, `MasterLayout`işlendiği başka bir düzene başvurur.</span><span class="sxs-lookup"><span data-stu-id="456a8-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="456a8-150">`EpisodesComponent`göründüğü yerde `@Body` işlenir:</span><span class="sxs-lookup"><span data-stu-id="456a8-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="456a8-151">Son `MasterLayout` olarak, *MasterLayout.razor'da* üstbilgi, ana menü ve altbilgi gibi üst düzey düzen öğeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="456a8-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="456a8-152">`MasterListLayout`göründüğü `EpisodesComponent` yerde `@Body` işlenir:</span><span class="sxs-lookup"><span data-stu-id="456a8-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="456a8-153">Bir Razor Pages düzenini tümleşik bileşenlerle paylaşma</span><span class="sxs-lookup"><span data-stu-id="456a8-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="456a8-154">Routable bileşenleri bir Razor Pages uygulamasına entegre edildiğinde, uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="456a8-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="456a8-155">Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="456a8-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="456a8-156">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="456a8-156">Additional resources</span></span>

* <xref:mvc/views/layout>
