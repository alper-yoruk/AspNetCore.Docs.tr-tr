---
title: Bölüm 3, yapı iskelesi Razor sayfaları
author: rick-anderson
description: Sayfalardaki eğitim serisinin 3. bölümü Razor .
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a6efbb22f8b6280bd636cd1575d8a4a2bca0bb06
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486180"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="345bc-103">Bölüm 3, ASP.NET Core yapı iskelesi Razor olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="345bc-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="345bc-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="345bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="345bc-105">Bu öğreticide Razor , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="345bc-106">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="345bc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="345bc-107">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="345bc-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="345bc-108">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="345bc-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="345bc-109">*Pages/filmler/ Index . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="345bc-110">Razor Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="345bc-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="345bc-111">Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="345bc-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="345bc-112">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `RazorPagesMovieContext` :</span><span class="sxs-lookup"><span data-stu-id="345bc-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="345bc-113">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="345bc-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="345bc-114">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="345bc-115">Sayfasında Razor , `OnGetAsync` veya `OnGet` sayfanın durumunu başlatmak için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="345bc-116">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="345bc-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="345bc-117">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , Return deyimleri kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="345bc-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="345bc-118">Örneğin gizlilik sayfası:</span><span class="sxs-lookup"><span data-stu-id="345bc-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="345bc-119">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="345bc-120">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="345bc-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="345bc-121">*Pages/filmler/ Index . cshtml* Razor sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="345bc-122">Razor HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="345bc-123">Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="345bc-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="345bc-124">@pageYönergesi</span><span class="sxs-lookup"><span data-stu-id="345bc-124">The @page directive</span></span>

<span data-ttu-id="345bc-125">`@page` Razor Yönergesi, dosyayı bir MVC eylemi yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="345bc-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="345bc-126">`@page` sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="345bc-127">`@page` ve `@model` belirli biçimlendirme örneklerine örnek olarak verilebilir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="345bc-128">Daha fazla bilgi için bkz. [ Razor sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="345bc-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="345bc-129">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="345bc-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="345bc-130">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="345bc-131">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="345bc-132">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="345bc-133">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="345bc-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="345bc-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="345bc-135">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="345bc-136">Bu,,, veya boş olduğunda bir erişim ihlali olmadığı anlamına gelir `model` `model.Movie` `model.Movie[0]` `null` .</span><span class="sxs-lookup"><span data-stu-id="345bc-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="345bc-137">Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="345bc-138">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="345bc-138">The layout page</span></span>

<span data-ttu-id="345bc-139">**Razor Pagesfilmi**, **ana sayfası** ve **gizliliği** bağlayan menüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="345bc-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="345bc-140">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="345bc-141">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="345bc-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="345bc-142">*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.</span><span class="sxs-lookup"><span data-stu-id="345bc-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="345bc-143">[Düzen](xref:mvc/views/layout) ŞABLONLARı, HTML kapsayıcı düzeninin şu şekilde olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="345bc-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="345bc-144">Tek bir yerde belirtildi.</span><span class="sxs-lookup"><span data-stu-id="345bc-144">Specified in one place.</span></span>
* <span data-ttu-id="345bc-145">Sitede birden çok sayfada uygulandı.</span><span class="sxs-lookup"><span data-stu-id="345bc-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="345bc-146">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="345bc-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="345bc-147">`RenderBody` , sayfaya özgü tüm görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="345bc-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="345bc-148">Örneğin, **Gizlilik** bağlantısını seçin ve *Sayfalar/gizlilik. cshtml* görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="345bc-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="345bc-149">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="345bc-149">ViewData and layout</span></span>

<span data-ttu-id="345bc-150">*Sayfalar/filmler/ Index . cshtml* dosyasından aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="345bc-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="345bc-151">Önceki vurgulanan biçimlendirme C# ' ye geçme örneğidir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="345bc-152">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="345bc-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="345bc-153">`PageModel`Temel sınıf, `ViewData` verileri bir görünüme geçirmek için kullanılabilecek bir Dictionary özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="345bc-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="345bc-154">Nesneler, `ViewData` bir \***anahtar değeri** _ düzeniyle kullanılarak sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="345bc-155">Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="345bc-156">`Title`Özelliği, _Pages/Shared/_Layout. cshtml \* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="345bc-157">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="345bc-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="345bc-158">Satır `@*Markup removed for brevity.*@` bir Razor açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="345bc-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="345bc-159">HTML yorumlarından farklı olarak `<!-- -->` , Razor açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="345bc-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="345bc-160">Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.</span><span class="sxs-lookup"><span data-stu-id="345bc-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="345bc-161">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="345bc-161">Update the layout</span></span>

1. <span data-ttu-id="345bc-162">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **Razor pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="345bc-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="345bc-163">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="345bc-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="345bc-164">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="345bc-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="345bc-165">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="345bc-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="345bc-166">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="345bc-167">`asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="345bc-168">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="345bc-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="345bc-169">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="345bc-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="345bc-170">**Rpmovie** bağlantısını seçerek değişiklikleri kaydedin ve uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="345bc-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="345bc-171">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="345bc-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="345bc-172">**Giriş**, **rpmovie**, **oluşturma**, **düzenleme** ve **silme** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="345bc-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="345bc-173">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="345bc-174">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="345bc-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="345bc-175">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="345bc-176">Ondalık virgülden ekleme hakkında yönergeler için bkz. [GitHub sorunu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="345bc-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="345bc-177">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="345bc-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="345bc-178">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar Razor . </span><span class="sxs-lookup"><span data-stu-id="345bc-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="345bc-179">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="345bc-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="345bc-180">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="345bc-180">The Create page model</span></span>

<span data-ttu-id="345bc-181">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="345bc-182">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="345bc-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="345bc-183">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="345bc-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="345bc-184">Öğreticide daha sonra, `OnGet` başlatma durumuna bir örnek gösterilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="345bc-185">`Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="345bc-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="345bc-186">`Movie`Özelliği, [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="345bc-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="345bc-187">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="345bc-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="345bc-188">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="345bc-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="345bc-189">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="345bc-190">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="345bc-191">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="345bc-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="345bc-192">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="345bc-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="345bc-193">Model hatası yoksa:</span><span class="sxs-lookup"><span data-stu-id="345bc-193">If there are no model errors:</span></span>

* <span data-ttu-id="345bc-194">Veriler kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-194">The data is saved.</span></span>
* <span data-ttu-id="345bc-195">Tarayıcı Index sayfaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="345bc-196">Oluştur Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="345bc-196">The Create Razor Page</span></span>

<span data-ttu-id="345bc-197">*Sayfalar/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="345bc-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="345bc-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="345bc-199">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="345bc-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create. cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="345bc-201">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="345bc-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="345bc-202">Aşağıdaki etiket yardımcıları, önceki biçimlendirmede gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="345bc-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="345bc-203">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="345bc-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="345bc-204">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="345bc-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="345bc-205">Yapı iskelesi altyapısı, Razor modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="345bc-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="345bc-206">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="345bc-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="345bc-207">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="345bc-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="345bc-208">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="345bc-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="345bc-209">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="345bc-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="345bc-210">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="345bc-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="345bc-211">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="345bc-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="345bc-212">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="345bc-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="345bc-213">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="345bc-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="345bc-214">*Pages/filmler/ Index . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="345bc-215">Razor Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="345bc-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="345bc-216">Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="345bc-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="345bc-217">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `RazorPagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="345bc-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="345bc-218">Yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="345bc-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="345bc-219">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="345bc-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="345bc-220">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="345bc-221">`OnGetAsync` ya da sayfa `OnGet` Razor için durumu başlatmak üzere bir sayfada çağırılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="345bc-222">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="345bc-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="345bc-223">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , return yöntemi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="345bc-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="345bc-224">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="345bc-225">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="345bc-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="345bc-226">*Pages/filmler/ Index . cshtml* Razor sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="345bc-227">Razor HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="345bc-228">Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="345bc-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="345bc-229">`@page` Razor Yönergesi, dosyayı bir MVC eylemine yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="345bc-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="345bc-230">`@page` sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="345bc-231">`@page` , belirli bir biçimlendirmeyi geçme örneğidir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="345bc-232">Daha fazla bilgi için bkz. [ Razor sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="345bc-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="345bc-233">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="345bc-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="345bc-234">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="345bc-235">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="345bc-236">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="345bc-237">HTML Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="345bc-237">HTML Helpers</span></span>

<span data-ttu-id="345bc-238">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="345bc-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="345bc-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="345bc-240">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="345bc-241">Diğer bir deyişle `model` ,, `model.Movie` , veya boş olduğunda erişim ihlali `model.Movie[0]` yoktur `null` .</span><span class="sxs-lookup"><span data-stu-id="345bc-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="345bc-242">Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="345bc-243">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="345bc-243">The layout page</span></span>

<span data-ttu-id="345bc-244">**Razor Pagesfilmi**, **ana sayfası** ve **gizliliği** bağlayan menüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="345bc-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="345bc-245">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="345bc-246">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="345bc-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="345bc-247">*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.</span><span class="sxs-lookup"><span data-stu-id="345bc-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="345bc-248">[Düzen](xref:mvc/views/layout) şablonları, BIR sitenin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitede birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="345bc-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="345bc-249">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="345bc-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="345bc-250">`RenderBody` , oluşturduğunuz tüm sayfaya özgü görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="345bc-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="345bc-251">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Sayfa/Gizlilik. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="345bc-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="345bc-252">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="345bc-252">ViewData and layout</span></span>

<span data-ttu-id="345bc-253">*Sayfalar/filmler/ Index . cshtml* dosyasından aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="345bc-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="345bc-254">Önceki vurgulanan kod C# ' ye geçme örneğidir Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="345bc-255">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="345bc-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="345bc-256">`PageModel`Temel sınıfın, `ViewData` bir görünüme geçirmek istediğiniz verileri eklemek için kullanılabilecek bir Dictionary özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="345bc-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="345bc-257">`ViewData`Bir anahtar/değer örüntüsünün kullanıldığı sözlüğe nesneler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="345bc-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="345bc-258">Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="345bc-259">`Title`Özelliği *Pages/Shared/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="345bc-260">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="345bc-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="345bc-261">Satır `@*Markup removed for brevity.*@` bir Razor açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="345bc-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="345bc-262">HTML yorumlarından farklı olarak `<!-- -->` , Razor açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="345bc-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="345bc-263">Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.</span><span class="sxs-lookup"><span data-stu-id="345bc-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="345bc-264">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="345bc-264">Update the layout</span></span>

<span data-ttu-id="345bc-265">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **Razor pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="345bc-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="345bc-266">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="345bc-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="345bc-267">Önceki öğeyi aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="345bc-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="345bc-268">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="345bc-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="345bc-269">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="345bc-270">`asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` Razor .</span><span class="sxs-lookup"><span data-stu-id="345bc-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="345bc-271">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="345bc-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="345bc-272">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="345bc-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="345bc-273">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="345bc-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="345bc-274">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="345bc-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="345bc-275">Diğer bağlantıları test edin (**giriş**, **rpmovie**, **oluşturma**, **düzenleme** ve **silme**).</span><span class="sxs-lookup"><span data-stu-id="345bc-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="345bc-276">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="345bc-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="345bc-277">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="345bc-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="345bc-278">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="345bc-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="345bc-279">Bu GitHub, ondalık virgülden ekleme hakkında yönergeler için [4076 sorun](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="345bc-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="345bc-280">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="345bc-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="345bc-281">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar Razor . </span><span class="sxs-lookup"><span data-stu-id="345bc-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="345bc-282">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="345bc-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="345bc-283">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="345bc-283">The Create page model</span></span>

<span data-ttu-id="345bc-284">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="345bc-285">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="345bc-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="345bc-286">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="345bc-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="345bc-287">Öğreticide daha sonra `OnGet` Yöntem başlatma durumunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="345bc-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="345bc-288">`Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="345bc-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="345bc-289">`Movie`Özelliği, <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]] özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="345bc-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="345bc-290">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="345bc-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="345bc-291">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="345bc-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="345bc-292">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="345bc-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="345bc-293">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="345bc-294">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="345bc-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="345bc-295">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="345bc-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="345bc-296">Model hatası yoksa, veriler kaydedilir ve tarayıcı Index sayfaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="345bc-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="345bc-297">Oluştur Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="345bc-297">The Create Razor Page</span></span>

<span data-ttu-id="345bc-298">*Sayfalar/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="345bc-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="345bc-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="345bc-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="345bc-300">Visual Studio etiketi, etiket `<form method="post">` yardımcıları için kullanılan farklı bir kalın yazı tipiyle görüntüler:</span><span class="sxs-lookup"><span data-stu-id="345bc-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create. cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="345bc-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="345bc-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="345bc-303">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="345bc-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="345bc-304">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="345bc-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="345bc-305">Mac için Visual Studio etiket `<form method="post">` yardımcıları için kullanılan farklı kalın yazı tipinde etiketi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="345bc-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="345bc-306">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="345bc-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="345bc-307">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="345bc-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="345bc-308">Yapı iskelesi altyapısı, Razor modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="345bc-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="345bc-309">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="345bc-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="345bc-310">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="345bc-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="345bc-311">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="345bc-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="345bc-312">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="345bc-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="345bc-313">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="345bc-313">Additional resources</span></span>

* [<span data-ttu-id="345bc-314">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="345bc-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="345bc-315">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="345bc-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
