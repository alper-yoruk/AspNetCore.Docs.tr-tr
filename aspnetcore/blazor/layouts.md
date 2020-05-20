---
title: ASP.NET Core Blazor düzenleri
author: guardrex
description: Uygulamalar için yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424329"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="0b296-103">ASP.NET Core Blazor düzenleri</span><span class="sxs-lookup"><span data-stu-id="0b296-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="0b296-104">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0b296-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0b296-105">Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0b296-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="0b296-106">Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak etkili bir yaklaşım değildir.</span><span class="sxs-lookup"><span data-stu-id="0b296-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="0b296-107">Öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0b296-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="0b296-108">Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="0b296-109">*Düzenler* bu sorunu çözüyor.</span><span class="sxs-lookup"><span data-stu-id="0b296-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="0b296-110">Teknik olarak, düzen yalnızca başka bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="0b296-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="0b296-111">Düzen bir Razor şablonda veya C# kodunda tanımlanır ve [veri bağlama](xref:blazor/data-binding), [bağımlılık ekleme](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="0b296-112">Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:</span><span class="sxs-lookup"><span data-stu-id="0b296-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="0b296-113">`LayoutComponentBase` `Body` Düzen içindeki işlenmiş içerik için bir özellik tanımlayan öğesinden devralır.</span><span class="sxs-lookup"><span data-stu-id="0b296-113">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="0b296-114">Razor `@Body` İçeriğin işlendiği yerleşim biçimlendirmesinde konumu belirtmek için söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0b296-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="0b296-115">Aşağıdaki kod örneğinde Razor , *mainlayout. Razor*olan bir düzen bileşeninin şablonu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0b296-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="0b296-116">Düzen, `LayoutComponentBase` `@Body` Gezinti çubuğu ve alt bilgi arasında devralır ve Ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0b296-116">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="0b296-117">Uygulama şablonlarından birini temel alan bir uygulamada Blazor , `MainLayout` bileşen (*mainlayout. Razor*) uygulamanın *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="0b296-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="0b296-118">Varsayılan düzen</span><span class="sxs-lookup"><span data-stu-id="0b296-118">Default layout</span></span>

<span data-ttu-id="0b296-119">`Router`Uygulamanın App *. Razor* dosyasındaki bileşende varsayılan uygulama yerleşimini belirtin.</span><span class="sxs-lookup"><span data-stu-id="0b296-119">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="0b296-120">`Router`Varsayılan Şablonlar tarafından belirtilen aşağıdaki bileşen, Blazor bileşene varsayılan düzeni ayarlar `MainLayout` :</span><span class="sxs-lookup"><span data-stu-id="0b296-120">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="0b296-121">İçerik için varsayılan bir düzen sağlamak üzere `NotFound` bir içerik belirtin `LayoutView` `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="0b296-121">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="0b296-122">Bileşen hakkında daha fazla bilgi için `Router` bkz <xref:blazor/routing> ..</span><span class="sxs-lookup"><span data-stu-id="0b296-122">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="0b296-123">Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="0b296-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="0b296-124">En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="0b296-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="0b296-125">Bir bileşende düzen belirtme</span><span class="sxs-lookup"><span data-stu-id="0b296-125">Specify a layout in a component</span></span>

<span data-ttu-id="0b296-126">Razor `@layout` Bir bileşene düzen uygulamak için yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0b296-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="0b296-127">Derleyici, `@layout` `LayoutAttribute` bileşen sınıfına uygulanan öğesine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="0b296-127">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="0b296-128">Aşağıdaki `MasterList` bileşenin içeriği konumuna öğesine eklenir `MasterLayout` `@Body` :</span><span class="sxs-lookup"><span data-stu-id="0b296-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="0b296-129">Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendiricide ayarlanan *varsayılan bir düzen* veya `@layout` *_Imports. Razor*'den içeri aktarılan bir yönerge geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="0b296-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="0b296-130">Merkezi düzen seçimi</span><span class="sxs-lookup"><span data-stu-id="0b296-130">Centralized layout selection</span></span>

<span data-ttu-id="0b296-131">Bir uygulamanın her klasörü, isteğe bağlı olarak *_Imports. Razor*adlı bir şablon dosyası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="0b296-132">Derleyici, içeri aktarmalar dosyasında belirtilen yönergeleri Razor aynı klasörde ve özyinelemeli olarak tüm alt klasörlerinde bulunan tüm şablonlarda içerir.</span><span class="sxs-lookup"><span data-stu-id="0b296-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="0b296-133">Bu nedenle, içeren bir *_Imports. Razor* dosyası, `@layout MyCoolLayout` bir klasördeki tüm bileşenlerin kullanımını sağlar `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="0b296-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="0b296-134">`@layout MyCoolLayout`Klasör ve alt klasörlerdeki tüm *. Razor* dosyalarına tekrar tekrar ekleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0b296-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="0b296-135">`@using`yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0b296-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="0b296-136">Aşağıdaki *_Imports. Razor* dosyası içeri aktarmaları:</span><span class="sxs-lookup"><span data-stu-id="0b296-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="0b296-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="0b296-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="0b296-138">RazorAynı klasörde ve tüm alt klasörlerde bulunan tüm bileşenler.</span><span class="sxs-lookup"><span data-stu-id="0b296-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="0b296-139">`BlazorApp1.Data`Ad alanı.</span><span class="sxs-lookup"><span data-stu-id="0b296-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="0b296-140">*_Imports. Razor* dosyası, [ Razor görünümler ve sayfalar için _ViewImports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzerdir, ancak özellikle Razor bileşen dosyalarına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0b296-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="0b296-141">_Imports bir düzen belirtme *. Razor* , yönlendiricinin *varsayılan düzeni*olarak belirtilen bir düzeni geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="0b296-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="0b296-142">İç içe düzenleri</span><span class="sxs-lookup"><span data-stu-id="0b296-142">Nested layouts</span></span>

<span data-ttu-id="0b296-143">Uygulamalar iç içe düzenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="0b296-144">Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="0b296-145">Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0b296-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="0b296-146">Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="0b296-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="0b296-147">*Epısodescomponent. Razor* dosyası görüntülenecek bileşendir.</span><span class="sxs-lookup"><span data-stu-id="0b296-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="0b296-148">Bileşen öğesine başvurur `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="0b296-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="0b296-149">*Masterlistlayout. Razor* dosyası sağlar `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="0b296-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="0b296-150">Düzen, nerede işlendiği başka bir düzene başvurur `MasterLayout` .</span><span class="sxs-lookup"><span data-stu-id="0b296-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="0b296-151">`EpisodesComponent`, görüntülendiği yerde işlenir `@Body` :</span><span class="sxs-lookup"><span data-stu-id="0b296-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="0b296-152">Son olarak `MasterLayout` , *masterlayout. Razor* içinde üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir.</span><span class="sxs-lookup"><span data-stu-id="0b296-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="0b296-153">`MasterListLayout`ile birlikte `EpisodesComponent` işlendiğinde, şu şekilde `@Body` görünür:</span><span class="sxs-lookup"><span data-stu-id="0b296-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="0b296-154">RazorTümleşik bileşenlerle bir sayfa düzeni paylaşma</span><span class="sxs-lookup"><span data-stu-id="0b296-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="0b296-155">Yönlendirilebilir bileşenler bir sayfalar uygulamasıyla tümleştirildiğinde Razor , uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0b296-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="0b296-156">Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="0b296-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b296-157">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0b296-157">Additional resources</span></span>

* <xref:mvc/views/layout>
