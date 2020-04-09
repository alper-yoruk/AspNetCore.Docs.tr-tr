---
title: ASP.NET Çekirdek'teki Alanlar
author: rick-anderson
description: Alanların, ilgili işlevleri ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak grup halinde düzenlemek için kullanılan ASP.NET bir MVC özelliğinin nasıl olduğunu öğrenin.
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625924"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="4efd1-103">ASP.NET Çekirdek'teki Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="4efd1-104">Yazar: [Dhananjay Kumar](https://twitter.com/debug_mode) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4efd1-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4efd1-105">Alanlar, ilgili işlevselliği ayrı olarak bir gruba düzenlemek için kullanılan ASP.NET bir özelliktir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="4efd1-106">Yönlendirme için ad alanı.</span><span class="sxs-lookup"><span data-stu-id="4efd1-106">Namespace for routing.</span></span>
* <span data-ttu-id="4efd1-107">Görünümler ve Jilet Sayfaları için klasör yapısı.</span><span class="sxs-lookup"><span data-stu-id="4efd1-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="4efd1-108">`area`Alanları kullanmak, başka bir rota parametresi, `controller` `action` yani Jilet Sayfası `page`ekleyerek yönlendirme amacıyla bir hiyerarşi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="4efd1-109">Alanlar, ASP.NET Core Web uygulamasını her biri kendi Jilet Sayfaları, denetleyicileri, görünümleri ve modelleri olan daha küçük işlevsel gruplara bölmenin bir yolunu sağlar.</span><span class="sxs-lookup"><span data-stu-id="4efd1-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="4efd1-110">Alan, etkin bir uygulama nın içindeki bir yapıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="4efd1-111">ASP.NET Core web projesinde, Sayfalar, Model, Denetleyici ve Görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="4efd1-112">ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="4efd1-113">Büyük bir uygulama için, uygulamayı ayrı üst düzey işlevsellik alanlarına bölmek avantajlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="4efd1-114">Örneğin, ödeme, faturalandırma ve arama gibi birden çok iş birimine sahip bir e-ticaret uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4efd1-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="4efd1-115">Bu birimlerin her birinin görünümleri, denetleyicileri, Jilet Sayfaları ve modelleri içeren kendi alanı vardır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="4efd1-116">Şu anda projedeki Alanları kullanmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="4efd1-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="4efd1-117">Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="4efd1-118">Uygulamayı, her işlevsel alanın bağımsız olarak çalışabilmesi için bölmek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="4efd1-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="4efd1-119">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4efd1-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4efd1-120">İndirme örneği, test alanları için temel bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="4efd1-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="4efd1-121">Jilet Sayfaları kullanıyorsanız, bu belgede [Jilet Li Alanlar'a](#areas-with-razor-pages) bakın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="4efd1-122">Görünüme sahip denetleyiciler için alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-122">Areas for controllers with views</span></span>

<span data-ttu-id="4efd1-123">Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core web uygulaması aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="4efd1-124">Alan [klasör yapısı.](#area-folder-structure)</span><span class="sxs-lookup"><span data-stu-id="4efd1-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="4efd1-125">Denetleyiciyi [`[Area]`](#attribute) alanla ilişkilendirecek öznitelikteki denetleyiciler:</span><span class="sxs-lookup"><span data-stu-id="4efd1-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="4efd1-126">[Başlangıç için eklenen alan rotası:](#add-area-route)</span><span class="sxs-lookup"><span data-stu-id="4efd1-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="4efd1-127">Alan klasör yapısı</span><span class="sxs-lookup"><span data-stu-id="4efd1-127">Area folder structure</span></span>

<span data-ttu-id="4efd1-128">İki mantıksal grubu olan bir uygulamayı düşünün, *Ürünler* ve *Hizmetler.*</span><span class="sxs-lookup"><span data-stu-id="4efd1-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="4efd1-129">Alanları kullanarak, klasör yapısı aşağıdakilere benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="4efd1-130">Proje adı</span><span class="sxs-lookup"><span data-stu-id="4efd1-130">Project name</span></span>
  * <span data-ttu-id="4efd1-131">Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-131">Areas</span></span>
    * <span data-ttu-id="4efd1-132">Ürünler</span><span class="sxs-lookup"><span data-stu-id="4efd1-132">Products</span></span>
      * <span data-ttu-id="4efd1-133">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="4efd1-133">Controllers</span></span>
        * <span data-ttu-id="4efd1-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-134">HomeController.cs</span></span>
        * <span data-ttu-id="4efd1-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-135">ManageController.cs</span></span>
      * <span data-ttu-id="4efd1-136">Görünümler</span><span class="sxs-lookup"><span data-stu-id="4efd1-136">Views</span></span>
        * <span data-ttu-id="4efd1-137">Ev</span><span class="sxs-lookup"><span data-stu-id="4efd1-137">Home</span></span>
          * <span data-ttu-id="4efd1-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-138">Index.cshtml</span></span>
        * <span data-ttu-id="4efd1-139">Yönetme</span><span class="sxs-lookup"><span data-stu-id="4efd1-139">Manage</span></span>
          * <span data-ttu-id="4efd1-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-140">Index.cshtml</span></span>
          * <span data-ttu-id="4efd1-141">Hakkında.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-141">About.cshtml</span></span>
    * <span data-ttu-id="4efd1-142">Hizmetler</span><span class="sxs-lookup"><span data-stu-id="4efd1-142">Services</span></span>
      * <span data-ttu-id="4efd1-143">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="4efd1-143">Controllers</span></span>
        * <span data-ttu-id="4efd1-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-144">HomeController.cs</span></span>
      * <span data-ttu-id="4efd1-145">Görünümler</span><span class="sxs-lookup"><span data-stu-id="4efd1-145">Views</span></span>
        * <span data-ttu-id="4efd1-146">Ev</span><span class="sxs-lookup"><span data-stu-id="4efd1-146">Home</span></span>
          * <span data-ttu-id="4efd1-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-147">Index.cshtml</span></span>

<span data-ttu-id="4efd1-148">Alanlar kullanılırken önceki düzen tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="4efd1-149">Eşleşen bir alan görünümü dosyasını aşağıdaki sırada bulma aramalarını görüntüle:</span><span class="sxs-lookup"><span data-stu-id="4efd1-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="4efd1-150">Denetleyiciyi bir Alanla ilişkilendirin</span><span class="sxs-lookup"><span data-stu-id="4efd1-150">Associate the controller with an Area</span></span>

<span data-ttu-id="4efd1-151">Alan denetleyicileri [ &lbrack;Alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği ile belirlenir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="4efd1-152">Alan rota ekle</span><span class="sxs-lookup"><span data-stu-id="4efd1-152">Add Area route</span></span>

<span data-ttu-id="4efd1-153">Alan yolları genellikle [öznitelik yönlendirmeyerine](xref:mvc/controllers/routing#ar) [geleneksel yönlendirme](xref:mvc/controllers/routing#cr) kullanın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="4efd1-154">Konvansiyonel yönlendirme isteðe baðlð±r.</span><span class="sxs-lookup"><span data-stu-id="4efd1-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4efd1-155">Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4efd1-156">`{area:...}`url alanı tüm alanlarda tek düze yse rota şablonlarında belirteç olarak kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="4efd1-157">Önceki kodda, `exists` rotanın bir alanla eşleşmesi gereken bir kısıtlama uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="4efd1-158">`MapControllerRoute`Kullanarak: `{area:...}`</span><span class="sxs-lookup"><span data-stu-id="4efd1-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="4efd1-159">Alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="4efd1-160">Tüm denetleyicileri öznitelik ile `[Area("Area name")]` eşleşir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="4efd1-161">Aşağıdaki kod, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> iki adlandırılmış alan yolu oluşturmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="4efd1-162">Daha fazla bilgi için [Bkz. Alan](xref:mvc/controllers/routing#areas)yönlendirmesi.</span><span class="sxs-lookup"><span data-stu-id="4efd1-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="4efd1-163">MVC alanları ile bağlantı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-163">Link generation with MVC areas</span></span>

<span data-ttu-id="4efd1-164">[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) aşağıdaki kod, belirtilen alana bağlantı oluşturmayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4efd1-165">Örnek karşıdan yükleme, aşağıdakileri içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="4efd1-166">Önceki bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="4efd1-166">The preceding links.</span></span>
* <span data-ttu-id="4efd1-167">Önceki ne benzer bağlantılar `area` hariç belirtilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="4efd1-168">Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4efd1-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4efd1-169">Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki ve denetleyicideki bir sayfadan başvurulduğunda geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="4efd1-170">Alan veya denetleyici belirtilmediğinde, yönlendirme [ortam](xref:mvc/controllers/routing#ambient) değerlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="4efd1-171">Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4efd1-172">Örnek uygulama için birçok durumda, ortam değerlerini kullanarak alanı belirtmeyen biçimlendirme ile yanlış bağlantılar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="4efd1-173">Daha fazla bilgi [için, eylemleri denetlemek için Yönlendirme'ye](xref:mvc/controllers/routing)bakın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="4efd1-174">_ViewStart.cshtml dosyasını kullanan Alanlar için paylaşılan düzen</span><span class="sxs-lookup"><span data-stu-id="4efd1-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="4efd1-175">Uygulamanın tamamı için ortak bir düzeni paylaşmak için *_ViewStart.cshtml'i* [uygulama kökü klasöründe](#arf)tutun.</span><span class="sxs-lookup"><span data-stu-id="4efd1-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="4efd1-176">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4efd1-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="4efd1-177">Uygulama kök klasörü</span><span class="sxs-lookup"><span data-stu-id="4efd1-177">Application root folder</span></span>

<span data-ttu-id="4efd1-178">Uygulama kökü klasörü, ASP.NET Core şablonları ile oluşturulan web uygulamasında *Startup.cs* içeren klasördür.</span><span class="sxs-lookup"><span data-stu-id="4efd1-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="4efd1-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="4efd1-180">*/Views/_ViewImports.cshtml*, MVC için ve */Pages/_ViewImports.cshtml* for Razor Pages, alanlardaki görünümlere aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="4efd1-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="4efd1-181">Tüm görünümlere görünüm içeriaklarını sağlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4efd1-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="4efd1-182">[Uygulama kök klasörüne](#arf) *_ViewImports.cshtml* ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4efd1-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="4efd1-183">Uygulama kökü klasöründeki *bir _ViewImports.cshtml* uygulamadaki tüm görünümler için geçerli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="4efd1-184">*_ViewImports.cshtml* dosyasını alanların altındaki uygun görünüm klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="4efd1-185">*_ViewImports.cshtml* dosyası genellikle [Tag Helpers](xref:mvc/views/tag-helpers/intro) `@using`içeri `@inject` aktarımları ve deyimleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="4efd1-186">Daha fazla bilgi için [bkz.](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="4efd1-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="4efd1-187">Görünümlerin depolandığı varsayılan alan klasörünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="4efd1-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="4efd1-188">Aşağıdaki kod varsayılan alan klasörünü aşağıdakilerden `"Areas"` şu şekilde `"MyAreas"`değiştirir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="4efd1-189">Jilet Sayfalı Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-189">Areas with Razor Pages</span></span>

<span data-ttu-id="4efd1-190">Razor Pages içeren `Areas/<area name>/Pages` alanlar, uygulamanın kökünde bir klasör gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="4efd1-191">Örnek [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)ile aşağıdaki klasör yapısı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="4efd1-192">Proje adı</span><span class="sxs-lookup"><span data-stu-id="4efd1-192">Project name</span></span>
  * <span data-ttu-id="4efd1-193">Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-193">Areas</span></span>
    * <span data-ttu-id="4efd1-194">Ürünler</span><span class="sxs-lookup"><span data-stu-id="4efd1-194">Products</span></span>
      * <span data-ttu-id="4efd1-195">Sayfalar</span><span class="sxs-lookup"><span data-stu-id="4efd1-195">Pages</span></span>
        * <span data-ttu-id="4efd1-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="4efd1-196">_ViewImports</span></span>
        * <span data-ttu-id="4efd1-197">Hakkında</span><span class="sxs-lookup"><span data-stu-id="4efd1-197">About</span></span>
        * <span data-ttu-id="4efd1-198">Dizin oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-198">Index</span></span>
    * <span data-ttu-id="4efd1-199">Hizmetler</span><span class="sxs-lookup"><span data-stu-id="4efd1-199">Services</span></span>
      * <span data-ttu-id="4efd1-200">Sayfalar</span><span class="sxs-lookup"><span data-stu-id="4efd1-200">Pages</span></span>
        * <span data-ttu-id="4efd1-201">Yönetme</span><span class="sxs-lookup"><span data-stu-id="4efd1-201">Manage</span></span>
          * <span data-ttu-id="4efd1-202">Hakkında</span><span class="sxs-lookup"><span data-stu-id="4efd1-202">About</span></span>
          * <span data-ttu-id="4efd1-203">Dizin oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="4efd1-204">Razor Pages ve alanları ile bağlantı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="4efd1-205">[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kod, belirtilen alana sahip bağlantı `asp-area="Products"`oluşturmayı gösterir (örneğin,):</span><span class="sxs-lookup"><span data-stu-id="4efd1-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4efd1-206">Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4efd1-207">Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4efd1-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4efd1-208">Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvurulduğunda geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="4efd1-209">Alan belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4efd1-210">Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4efd1-211">Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="4efd1-212">Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4efd1-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="4efd1-213">Önceki kod için:</span><span class="sxs-lookup"><span data-stu-id="4efd1-213">For the preceding code:</span></span>

* <span data-ttu-id="4efd1-214">Oluşturulan bağlantı `<a asp-page="/Manage/About">` yalnızca son istek alandaki `Services` bir sayfa için olduğunda doğrudur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="4efd1-215">Örneğin, `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`veya .</span><span class="sxs-lookup"><span data-stu-id="4efd1-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="4efd1-216">Oluşturulan bağlantı `<a asp-page="/About">` yalnızca son istek bir sayfa için `/Home`olduğunda doğrudur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="4efd1-217">Kod [örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="4efd1-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="4efd1-218">_ViewImports dosyayla ad alanı ve Tag Yardımcıları alma</span><span class="sxs-lookup"><span data-stu-id="4efd1-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="4efd1-219">Klasördeki her Razor Page'e ad alanı ve Tag Helpers'ı almak için her alan *Sayfaları* klasörüne *bir _ViewImports.cshtml* dosyası eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="4efd1-220">Örnek kodun *_ViewImports.cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="4efd1-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4efd1-221">Aşağıdaki biçimlendirme */Hizmetler/Yönet/Hakkında* Jilet Sayfasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="4efd1-222">Önceki biçimlendirmede:</span><span class="sxs-lookup"><span data-stu-id="4efd1-222">In the preceding markup:</span></span>

* <span data-ttu-id="4efd1-223">Modeli belirtmek için tam nitelikli alan adı`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kullanılmalıdır ( ).</span><span class="sxs-lookup"><span data-stu-id="4efd1-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="4efd1-224">[Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="4efd1-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="4efd1-225">Örnek indirmede, Ürünler alanı aşağıdaki *_ViewImports.cshtml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4efd1-226">Aşağıdaki biçimlendirme */Ürünler/Jilet Hakkında* Sayfayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="4efd1-227">Önceki dosyada, ad alanı `@addTagHelper` ve yönerge *Alanlar/Ürünler/Sayfalar/_ViewImports.cshtml* dosyası tarafından dosyaya aktarılır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="4efd1-228">Daha fazla bilgi için [Bkz. Etiket Yardımcısı kapsamını Yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve Paylaşılan Yönergeleri [Alma.](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="4efd1-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="4efd1-229">Jilet Sayfaları Alanları için paylaşılan düzen</span><span class="sxs-lookup"><span data-stu-id="4efd1-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="4efd1-230">Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="4efd1-231">Yayın Alanları</span><span class="sxs-lookup"><span data-stu-id="4efd1-231">Publishing Areas</span></span>

<span data-ttu-id="4efd1-232">*Wwwroot* dizinindeki tüm \*.cshtml dosyaları ve dosyaları `<Project Sdk="Microsoft.NET.Sdk.Web">` \*.csproj dosyasına dahil edildiğinde çıktıya yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4efd1-233">Alanlar, ilgili işlevleri ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak grup halinde düzenlemek için kullanılan ASP.NET bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="4efd1-234">`area`Alanları kullanmak, başka bir rota parametresi, `controller` `action` yani Jilet Sayfası `page`ekleyerek yönlendirme amacıyla bir hiyerarşi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="4efd1-235">Alanlar, ASP.NET Core Web uygulamasını her biri kendi Jilet Sayfaları, denetleyicileri, görünümleri ve modelleri olan daha küçük işlevsel gruplara bölmenin bir yolunu sağlar.</span><span class="sxs-lookup"><span data-stu-id="4efd1-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="4efd1-236">Alan, etkin bir uygulama nın içindeki bir yapıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="4efd1-237">ASP.NET Core web projesinde, Sayfalar, Model, Denetleyici ve Görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="4efd1-238">ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="4efd1-239">Büyük bir uygulama için, uygulamayı ayrı üst düzey işlevsellik alanlarına bölmek avantajlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="4efd1-240">Örneğin, ödeme, faturalandırma ve arama gibi birden çok iş birimine sahip bir e-ticaret uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4efd1-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="4efd1-241">Bu birimlerin her birinin görünümleri, denetleyicileri, Jilet Sayfaları ve modelleri içeren kendi alanı vardır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="4efd1-242">Şu anda projedeki Alanları kullanmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="4efd1-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="4efd1-243">Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="4efd1-244">Uygulamayı, her işlevsel alanın bağımsız olarak çalışabilmesi için bölmek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="4efd1-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="4efd1-245">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4efd1-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4efd1-246">İndirme örneği, test alanları için temel bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="4efd1-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="4efd1-247">Jilet Sayfaları kullanıyorsanız, bu belgede [Jilet Li Alanlar'a](#areas-with-razor-pages) bakın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="4efd1-248">Görünüme sahip denetleyiciler için alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-248">Areas for controllers with views</span></span>

<span data-ttu-id="4efd1-249">Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core web uygulaması aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="4efd1-250">Alan [klasör yapısı.](#area-folder-structure)</span><span class="sxs-lookup"><span data-stu-id="4efd1-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="4efd1-251">Denetleyiciyi [`[Area]`](#attribute) alanla ilişkilendirecek öznitelikteki denetleyiciler:</span><span class="sxs-lookup"><span data-stu-id="4efd1-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="4efd1-252">[Başlangıç için eklenen alan rotası:](#add-area-route)</span><span class="sxs-lookup"><span data-stu-id="4efd1-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="4efd1-253">Alan klasör yapısı</span><span class="sxs-lookup"><span data-stu-id="4efd1-253">Area folder structure</span></span>

<span data-ttu-id="4efd1-254">İki mantıksal grubu olan bir uygulamayı düşünün, *Ürünler* ve *Hizmetler.*</span><span class="sxs-lookup"><span data-stu-id="4efd1-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="4efd1-255">Alanları kullanarak, klasör yapısı aşağıdakilere benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="4efd1-256">Proje adı</span><span class="sxs-lookup"><span data-stu-id="4efd1-256">Project name</span></span>
  * <span data-ttu-id="4efd1-257">Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-257">Areas</span></span>
    * <span data-ttu-id="4efd1-258">Ürünler</span><span class="sxs-lookup"><span data-stu-id="4efd1-258">Products</span></span>
      * <span data-ttu-id="4efd1-259">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="4efd1-259">Controllers</span></span>
        * <span data-ttu-id="4efd1-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-260">HomeController.cs</span></span>
        * <span data-ttu-id="4efd1-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-261">ManageController.cs</span></span>
      * <span data-ttu-id="4efd1-262">Görünümler</span><span class="sxs-lookup"><span data-stu-id="4efd1-262">Views</span></span>
        * <span data-ttu-id="4efd1-263">Ev</span><span class="sxs-lookup"><span data-stu-id="4efd1-263">Home</span></span>
          * <span data-ttu-id="4efd1-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-264">Index.cshtml</span></span>
        * <span data-ttu-id="4efd1-265">Yönetme</span><span class="sxs-lookup"><span data-stu-id="4efd1-265">Manage</span></span>
          * <span data-ttu-id="4efd1-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-266">Index.cshtml</span></span>
          * <span data-ttu-id="4efd1-267">Hakkında.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-267">About.cshtml</span></span>
    * <span data-ttu-id="4efd1-268">Hizmetler</span><span class="sxs-lookup"><span data-stu-id="4efd1-268">Services</span></span>
      * <span data-ttu-id="4efd1-269">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="4efd1-269">Controllers</span></span>
        * <span data-ttu-id="4efd1-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4efd1-270">HomeController.cs</span></span>
      * <span data-ttu-id="4efd1-271">Görünümler</span><span class="sxs-lookup"><span data-stu-id="4efd1-271">Views</span></span>
        * <span data-ttu-id="4efd1-272">Ev</span><span class="sxs-lookup"><span data-stu-id="4efd1-272">Home</span></span>
          * <span data-ttu-id="4efd1-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-273">Index.cshtml</span></span>

<span data-ttu-id="4efd1-274">Alanlar kullanılırken önceki düzen tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="4efd1-275">Eşleşen bir alan görünümü dosyasını aşağıdaki sırada bulma aramalarını görüntüle:</span><span class="sxs-lookup"><span data-stu-id="4efd1-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="4efd1-276">Denetleyiciyi bir Alanla ilişkilendirin</span><span class="sxs-lookup"><span data-stu-id="4efd1-276">Associate the controller with an Area</span></span>

<span data-ttu-id="4efd1-277">Alan denetleyicileri [ &lbrack;Alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği ile belirlenir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="4efd1-278">Alan rota ekle</span><span class="sxs-lookup"><span data-stu-id="4efd1-278">Add Area route</span></span>

<span data-ttu-id="4efd1-279">Alan yolları genellikle öznitelik yönlendirmeyerine geleneksel yönlendirme kullanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="4efd1-280">Konvansiyonel yönlendirme isteðe baðlð±r.</span><span class="sxs-lookup"><span data-stu-id="4efd1-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4efd1-281">Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4efd1-282">`{area:...}`url alanı tüm alanlarda tek düze yse rota şablonlarında belirteç olarak kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="4efd1-283">Önceki kodda, `exists` rotanın bir alanla eşleşmesi gereken bir kısıtlama uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="4efd1-284">Kullanma, `{area:...}` alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="4efd1-285">Aşağıdaki kod, <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> iki adlandırılmış alan yolu oluşturmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="4efd1-286">core `MapAreaRoute` 2.2 ASP.NET kullanırken, [bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/7772)bakın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="4efd1-287">Daha fazla bilgi için [Bkz. Alan](xref:mvc/controllers/routing#areas)yönlendirmesi.</span><span class="sxs-lookup"><span data-stu-id="4efd1-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="4efd1-288">MVC alanları ile bağlantı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-288">Link generation with MVC areas</span></span>

<span data-ttu-id="4efd1-289">[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) aşağıdaki kod, belirtilen alana bağlantı oluşturmayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4efd1-290">Önceki kodla oluşturulan bağlantılar uygulamanın her yerinde geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="4efd1-291">Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4efd1-292">Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4efd1-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4efd1-293">Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki ve denetleyicideki bir sayfadan başvurulduğunda geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="4efd1-294">Alan veya denetleyici belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4efd1-295">Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4efd1-296">Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="4efd1-297">Daha fazla bilgi [için, eylemleri denetlemek için Yönlendirme'ye](xref:mvc/controllers/routing)bakın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="4efd1-298">_ViewStart.cshtml dosyasını kullanan Alanlar için paylaşılan düzen</span><span class="sxs-lookup"><span data-stu-id="4efd1-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="4efd1-299">Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="4efd1-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="4efd1-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="4efd1-301">Standart konumunda *, /Views/_ViewImports.cshtml* alanlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="4efd1-302">Yaygın [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanmak `@using`için `@inject` , veya bölgenizde, uygun bir *_ViewImports.cshtml* dosyası [bölge görünümleriniz için geçerli olduğundan](xref:mvc/views/layout#importing-shared-directives)emin olun.</span><span class="sxs-lookup"><span data-stu-id="4efd1-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="4efd1-303">Tüm görünümlerinizde aynı davranışı istiyorsanız, */Views/_ViewImports.cshtml'i* uygulama köküne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="4efd1-304">Görünümlerin depolandığı varsayılan alan klasörünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="4efd1-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="4efd1-305">Aşağıdaki kod varsayılan alan klasörünü aşağıdakilerden `"Areas"` şu şekilde `"MyAreas"`değiştirir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="4efd1-306">Jilet Sayfalı Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-306">Areas with Razor Pages</span></span>

<span data-ttu-id="4efd1-307">Razor Pages içeren `Areas/<area name>/Pages` alanlar, uygulamanın kökünde bir klasör gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="4efd1-308">Örnek [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)ile aşağıdaki klasör yapısı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4efd1-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="4efd1-309">Proje adı</span><span class="sxs-lookup"><span data-stu-id="4efd1-309">Project name</span></span>
  * <span data-ttu-id="4efd1-310">Alanlar</span><span class="sxs-lookup"><span data-stu-id="4efd1-310">Areas</span></span>
    * <span data-ttu-id="4efd1-311">Ürünler</span><span class="sxs-lookup"><span data-stu-id="4efd1-311">Products</span></span>
      * <span data-ttu-id="4efd1-312">Sayfalar</span><span class="sxs-lookup"><span data-stu-id="4efd1-312">Pages</span></span>
        * <span data-ttu-id="4efd1-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="4efd1-313">_ViewImports</span></span>
        * <span data-ttu-id="4efd1-314">Hakkında</span><span class="sxs-lookup"><span data-stu-id="4efd1-314">About</span></span>
        * <span data-ttu-id="4efd1-315">Dizin oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-315">Index</span></span>
    * <span data-ttu-id="4efd1-316">Hizmetler</span><span class="sxs-lookup"><span data-stu-id="4efd1-316">Services</span></span>
      * <span data-ttu-id="4efd1-317">Sayfalar</span><span class="sxs-lookup"><span data-stu-id="4efd1-317">Pages</span></span>
        * <span data-ttu-id="4efd1-318">Yönetme</span><span class="sxs-lookup"><span data-stu-id="4efd1-318">Manage</span></span>
          * <span data-ttu-id="4efd1-319">Hakkında</span><span class="sxs-lookup"><span data-stu-id="4efd1-319">About</span></span>
          * <span data-ttu-id="4efd1-320">Dizin oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="4efd1-321">Razor Pages ve alanları ile bağlantı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4efd1-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="4efd1-322">[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kod, belirtilen alana sahip bağlantı `asp-area="Products"`oluşturmayı gösterir (örneğin,):</span><span class="sxs-lookup"><span data-stu-id="4efd1-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4efd1-323">Önceki kodla oluşturulan bağlantılar uygulamanın her yerinde geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="4efd1-324">Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4efd1-325">Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4efd1-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4efd1-326">Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvurulduğunda geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="4efd1-327">Alan belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4efd1-328">Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4efd1-329">Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="4efd1-330">Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4efd1-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="4efd1-331">Önceki kod için:</span><span class="sxs-lookup"><span data-stu-id="4efd1-331">For the preceding code:</span></span>

* <span data-ttu-id="4efd1-332">Oluşturulan bağlantı `<a asp-page="/Manage/About">` yalnızca son istek alandaki `Services` bir sayfa için olduğunda doğrudur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="4efd1-333">Örneğin, `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`veya .</span><span class="sxs-lookup"><span data-stu-id="4efd1-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="4efd1-334">Oluşturulan bağlantı `<a asp-page="/About">` yalnızca son istek bir sayfa için `/Home`olduğunda doğrudur.</span><span class="sxs-lookup"><span data-stu-id="4efd1-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="4efd1-335">Kod [örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="4efd1-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="4efd1-336">_ViewImports dosyayla ad alanı ve Tag Yardımcıları alma</span><span class="sxs-lookup"><span data-stu-id="4efd1-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="4efd1-337">Klasördeki her Razor Page'e ad alanı ve Tag Helpers'ı almak için her alan *Sayfaları* klasörüne *bir _ViewImports.cshtml* dosyası eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4efd1-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="4efd1-338">Örnek kodun *_ViewImports.cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="4efd1-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4efd1-339">Aşağıdaki biçimlendirme */Hizmetler/Yönet/Hakkında* Jilet Sayfasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="4efd1-340">Önceki biçimlendirmede:</span><span class="sxs-lookup"><span data-stu-id="4efd1-340">In the preceding markup:</span></span>

* <span data-ttu-id="4efd1-341">Modeli belirtmek için tam nitelikli alan adı`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kullanılmalıdır ( ).</span><span class="sxs-lookup"><span data-stu-id="4efd1-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="4efd1-342">[Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="4efd1-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="4efd1-343">Örnek indirmede, Ürünler alanı aşağıdaki *_ViewImports.cshtml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4efd1-344">Aşağıdaki biçimlendirme */Ürünler/Jilet Hakkında* Sayfayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="4efd1-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="4efd1-345">Önceki dosyada, ad alanı `@addTagHelper` ve yönerge *Alanlar/Ürünler/Sayfalar/_ViewImports.cshtml* dosyası tarafından dosyaya aktarılır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="4efd1-346">Daha fazla bilgi için [Bkz. Etiket Yardımcısı kapsamını Yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve Paylaşılan Yönergeleri [Alma.](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="4efd1-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="4efd1-347">Jilet Sayfaları Alanları için paylaşılan düzen</span><span class="sxs-lookup"><span data-stu-id="4efd1-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="4efd1-348">Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.</span><span class="sxs-lookup"><span data-stu-id="4efd1-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="4efd1-349">Yayın Alanları</span><span class="sxs-lookup"><span data-stu-id="4efd1-349">Publishing Areas</span></span>

<span data-ttu-id="4efd1-350">*Wwwroot* dizinindeki tüm \*.cshtml dosyaları ve dosyaları `<Project Sdk="Microsoft.NET.Sdk.Web">` \*.csproj dosyasına dahil edildiğinde çıktıya yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="4efd1-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
