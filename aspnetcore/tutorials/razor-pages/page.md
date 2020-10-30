---
title: 'Bölüm 3, ASP.NET Core yapı iskelesi :::no-loc(Razor)::: olan sayfalar'
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 3. bölümü :::no-loc(Razor)::: .'
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/page
ms.openlocfilehash: 7d0085e1d444de30ca124ef544668122ab350c93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060059"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="2c8fe-103">Bölüm 3, ASP.NET Core yapı iskelesi :::no-loc(Razor)::: olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="2c8fe-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c8fe-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c8fe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2c8fe-105">Bu öğreticide :::no-loc(Razor)::: , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="2c8fe-106">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="2c8fe-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="2c8fe-107">*Pages/filmler/Index. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="2c8fe-108">:::no-loc(Razor)::: Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-108">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="2c8fe-109">Kural gereği, `PageModel` -türetilmiş sınıfı çağrılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="2c8fe-110">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `:::no-loc(Razor):::PagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="2c8fe-111">Tüm yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="2c8fe-112">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="2c8fe-113">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-114">`OnGetAsync` ya da `OnGet` sayfanın durumunu başlatmak için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="2c8fe-115">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="2c8fe-116">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , Return deyimleri kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="2c8fe-117">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="2c8fe-118">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="2c8fe-119">*Pages/filmler/Index. cshtml* :::no-loc(Razor)::: sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-119">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="2c8fe-120">:::no-loc(Razor)::: HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-120">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="2c8fe-121">Bir `@` simgenin arkasından [ :::no-loc(Razor)::: ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir :::no-loc(Razor)::: biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-121">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="2c8fe-122">@pageYönergesi</span><span class="sxs-lookup"><span data-stu-id="2c8fe-122">The @page directive</span></span>

<span data-ttu-id="2c8fe-123">`@page` :::no-loc(Razor)::: Yönergesi, dosyayı bir MVC eylemi yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-123">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="2c8fe-124">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-124">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="2c8fe-125">`@page` , belirli bir biçimlendirmeyi geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-125">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="2c8fe-126">Daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-126">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="2c8fe-127">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="2c8fe-128">`DisplayNameFor`HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="2c8fe-129">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="2c8fe-130">Bu,,, veya boş olduğunda bir erişim ihlali olmadığı anlamına gelir `model` `model.Movie` `model.Movie[0]` `null` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="2c8fe-131">Lambda ifadesi değerlendirildiğinde (örneğin, ile `@Html.DisplayFor(modelItem => item.Title)` ), modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="2c8fe-132">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="2c8fe-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="2c8fe-133">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-133">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-134">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-135">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="2c8fe-136">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="2c8fe-136">The layout page</span></span>

<span data-ttu-id="2c8fe-137">Menü bağlantılarını ( **:::no-loc(Razor)::: pagesmovie** , **Home** ve **Gizlilik** ) seçin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-137">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="2c8fe-138">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="2c8fe-139">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="2c8fe-140">*Pages/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="2c8fe-141">[Düzen](xref:mvc/views/layout) ŞABLONLARı, HTML kapsayıcı düzeninin şu şekilde olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="2c8fe-142">Tek bir yerde belirtildi.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-142">Specified in one place.</span></span>
* <span data-ttu-id="2c8fe-143">Sitede birden çok sayfada uygulandı.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="2c8fe-144">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="2c8fe-145">`RenderBody` , sayfaya özgü tüm görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="2c8fe-146">Örneğin, **Gizlilik** bağlantısını seçin ve *Sayfalar/gizlilik. cshtml* görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="2c8fe-147">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="2c8fe-147">ViewData and layout</span></span>

<span data-ttu-id="2c8fe-148">*Pages/filmler/Index. cshtml* dosyasından aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="2c8fe-149">Önceki vurgulanan biçimlendirme C# ' ye geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-149">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="2c8fe-150">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="2c8fe-151">`PageModel`Temel sınıf, `ViewData` verileri bir görünüme geçirmek için kullanılabilecek bir Dictionary özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="2c8fe-152">Nesneler, `ViewData` anahtara/değer düzeniyle kullanılarak sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="2c8fe-153">Yukarıdaki örnekte, `"Title"` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="2c8fe-154">`"Title"`Özelliği *Pages/Shared/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="2c8fe-155">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="2c8fe-156">Satır `@*Markup removed for brevity.*@` bir :::no-loc(Razor)::: açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-156">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="2c8fe-157">HTML yorumlarının ( `<!-- -->` ) aksine, :::no-loc(Razor)::: açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-157">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="2c8fe-158">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2c8fe-158">Update the layout</span></span>

<span data-ttu-id="2c8fe-159">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **:::no-loc(Razor)::: pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="2c8fe-160">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="2c8fe-161">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="2c8fe-162">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="2c8fe-163">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="2c8fe-164">`asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-165">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="2c8fe-166">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="2c8fe-167">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="2c8fe-168">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="2c8fe-169">Diğer bağlantıları test edin ( **giriş** , **rpmovie** , **oluşturma** , **düzenleme** ve **silme** ).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-169">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="2c8fe-170">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="2c8fe-171">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2c8fe-172">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanızı globalize için adımlar uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="2c8fe-173">Ondalık virgülden ekleme hakkında yönergeler için bkz. [GitHub sorunu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="2c8fe-174">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="2c8fe-175">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar :::no-loc(Razor)::: . *Pages*</span><span class="sxs-lookup"><span data-stu-id="2c8fe-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="2c8fe-176">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="2c8fe-177">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="2c8fe-177">The Create page model</span></span>

<span data-ttu-id="2c8fe-178">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="2c8fe-179">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="2c8fe-180">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="2c8fe-181">Öğreticide daha sonra, `OnGet` başlatma durumuna bir örnek gösterilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="2c8fe-182">`Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="2c8fe-183">`Movie`Özelliği, `[BindProperty]` [model bağlamayı](xref:mvc/models/model-binding)kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="2c8fe-184">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="2c8fe-185">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="2c8fe-186">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="2c8fe-187">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="2c8fe-188">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="2c8fe-189">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="2c8fe-190">Model hatası yoksa, veriler kaydedilir ve tarayıcı dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="2c8fe-191">Oluştur :::no-loc(Razor)::: sayfası</span><span class="sxs-lookup"><span data-stu-id="2c8fe-191">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="2c8fe-192">*Sayfalar/filmler/Create. cshtml* :::no-loc(Razor)::: sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-192">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="2c8fe-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c8fe-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c8fe-194">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create. cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c8fe-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c8fe-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c8fe-197">Aşağıdaki etiket yardımcıları, önceki biçimlendirmede gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c8fe-198">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c8fe-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c8fe-199">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="2c8fe-200">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="2c8fe-201">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="2c8fe-202">Yapı iskelesi altyapısı, :::no-loc(Razor)::: modeldeki her bir alan için (kimlik dışında) aşağıdakine benzer bir biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-202">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="2c8fe-203">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="2c8fe-204">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="2c8fe-205">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `for` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="2c8fe-206">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="2c8fe-207">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c8fe-208">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c8fe-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2c8fe-209">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="2c8fe-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c8fe-210">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c8fe-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2c8fe-211">Bu öğreticide :::no-loc(Razor)::: , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-211">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="2c8fe-212">Örneği [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="2c8fe-213">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="2c8fe-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="2c8fe-214">*Pages/filmler/Index. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="2c8fe-215">:::no-loc(Razor)::: Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-215">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="2c8fe-216">Kural gereği, `PageModel` -türetilmiş sınıfı çağrılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="2c8fe-217">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `:::no-loc(Razor):::PagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="2c8fe-218">Tüm yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="2c8fe-219">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="2c8fe-220">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-221">`OnGetAsync` ya da sayfa `OnGet` :::no-loc(Razor)::: için durumu başlatmak üzere bir sayfada çağırılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-221">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="2c8fe-222">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="2c8fe-223">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , return yöntemi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="2c8fe-224">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="2c8fe-225">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="2c8fe-226">*Pages/filmler/Index. cshtml* :::no-loc(Razor)::: sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-226">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="2c8fe-227">:::no-loc(Razor)::: HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-227">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="2c8fe-228">Bir `@` simgenin arkasından [ :::no-loc(Razor)::: ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir :::no-loc(Razor)::: biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-228">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="2c8fe-229">`@page` :::no-loc(Razor)::: Yönergesi, dosyayı bir MVC eylemine yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-229">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="2c8fe-230">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-230">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="2c8fe-231">`@page` , belirli bir biçimlendirmeyi geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-231">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="2c8fe-232">Daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-232">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="2c8fe-233">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="2c8fe-234">`DisplayNameFor`HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="2c8fe-235">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="2c8fe-236">Diğer bir deyişle `model` ,, `model.Movie` , veya boş olduğunda erişim ihlali `model.Movie[0]` yoktur `null` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="2c8fe-237">Lambda ifadesi değerlendirildiğinde (örneğin, ile `@Html.DisplayFor(modelItem => item.Title)` ), modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="2c8fe-238">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="2c8fe-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="2c8fe-239">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-239">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-240">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-241">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="2c8fe-242">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="2c8fe-242">The layout page</span></span>

<span data-ttu-id="2c8fe-243">Menü bağlantılarını ( **:::no-loc(Razor)::: pagesmovie** , **Home** ve **Gizlilik** ) seçin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-243">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="2c8fe-244">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="2c8fe-245">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="2c8fe-246">*Pages/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="2c8fe-247">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="2c8fe-248">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="2c8fe-249">`RenderBody` , oluşturduğunuz tüm sayfaya özgü görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="2c8fe-250">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Sayfa/Gizlilik. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="2c8fe-251">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="2c8fe-251">ViewData and layout</span></span>

<span data-ttu-id="2c8fe-252">*Pages/filmler/Index. cshtml* dosyasından aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="2c8fe-253">Önceki vurgulanan kod C# ' ye geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-253">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="2c8fe-254">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="2c8fe-255">`PageModel`Temel sınıfın, `ViewData` bir görünüme geçirmek istediğiniz verileri eklemek için kullanılabilecek bir Dictionary özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="2c8fe-256">`ViewData`Bir anahtar/değer örüntüsünün kullanıldığı sözlüğe nesneler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="2c8fe-257">Yukarıdaki örnekte, "title" özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="2c8fe-258">"Title" özelliği *sayfa/paylaşılan/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="2c8fe-259">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="2c8fe-260">Çizgi, `@*Markup removed for brevity.*@` :::no-loc(Razor)::: Düzen dosyanızda görünmeyen bir açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-260">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="2c8fe-261">HTML yorumlarının ( `<!-- -->` ) aksine, :::no-loc(Razor)::: açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-261">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="2c8fe-262">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2c8fe-262">Update the layout</span></span>

<span data-ttu-id="2c8fe-263">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **:::no-loc(Razor)::: pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="2c8fe-264">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="2c8fe-265">Önceki öğeyi aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="2c8fe-266">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="2c8fe-267">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="2c8fe-268">`asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="2c8fe-269">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="2c8fe-270">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="2c8fe-271">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="2c8fe-272">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="2c8fe-273">Diğer bağlantıları test edin ( **giriş** , **rpmovie** , **oluşturma** , **düzenleme** ve **silme** ).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-273">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="2c8fe-274">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="2c8fe-275">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2c8fe-276">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanızı globalize için adımlar uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="2c8fe-277">Bu GitHub, ondalık virgülden ekleme hakkında yönergeler için [4076 sorun](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="2c8fe-278">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="2c8fe-279">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar :::no-loc(Razor)::: . *Pages*</span><span class="sxs-lookup"><span data-stu-id="2c8fe-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="2c8fe-280">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="2c8fe-281">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="2c8fe-281">The Create page model</span></span>

<span data-ttu-id="2c8fe-282">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="2c8fe-283">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="2c8fe-284">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="2c8fe-285">Öğreticide daha sonra `OnGet` Yöntem başlatma durumunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="2c8fe-286">`Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="2c8fe-287">`Movie`Özelliği, `[BindProperty]` [model bağlamayı](xref:mvc/models/model-binding)kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="2c8fe-288">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="2c8fe-289">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="2c8fe-290">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="2c8fe-291">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="2c8fe-292">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="2c8fe-293">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="2c8fe-294">Model hatası yoksa, veriler kaydedilir ve tarayıcı dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="2c8fe-295">Oluştur :::no-loc(Razor)::: sayfası</span><span class="sxs-lookup"><span data-stu-id="2c8fe-295">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="2c8fe-296">*Sayfalar/filmler/Create. cshtml* :::no-loc(Razor)::: sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-296">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="2c8fe-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c8fe-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c8fe-298">Visual Studio etiketi, etiket `<form method="post">` yardımcıları için kullanılan farklı bir kalın yazı tipiyle görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create. cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c8fe-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c8fe-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c8fe-301">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c8fe-302">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c8fe-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c8fe-303">Mac için Visual Studio etiket `<form method="post">` yardımcıları için kullanılan farklı kalın yazı tipinde etiketi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="2c8fe-304">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2c8fe-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="2c8fe-305">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="2c8fe-306">Yapı iskelesi altyapısı, :::no-loc(Razor)::: modeldeki her bir alan için (kimlik dışında) aşağıdakine benzer bir biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2c8fe-306">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="2c8fe-307">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="2c8fe-308">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="2c8fe-309">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `for` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="2c8fe-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="2c8fe-310">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="2c8fe-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c8fe-311">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c8fe-311">Additional resources</span></span>

* [<span data-ttu-id="2c8fe-312">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="2c8fe-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="2c8fe-313">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="2c8fe-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
