---
title: 'Bölüm 3, yapı iskelesi :::no-loc(Razor)::: sayfaları'
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 3. bölümü :::no-loc(Razor)::: .'
ms.author: riande
ms.date: 09/25/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
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
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360898"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="e91c2-103">Bölüm 3, ASP.NET Core yapı iskelesi :::no-loc(Razor)::: olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="e91c2-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="e91c2-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e91c2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e91c2-105">Bu öğreticide :::no-loc(Razor)::: , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e91c2-106">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e91c2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="e91c2-107">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e91c2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="e91c2-108">:::no-loc(Create):::,, :::no-loc(Delete)::: Ayrıntıları ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="e91c2-108">The :::no-loc(Create):::, :::no-loc(Delete):::, Details, and Edit pages</span></span>

<span data-ttu-id="e91c2-109">*Pages/filmler/ :::no-loc(Index)::: . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-109">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml.cs)]

<span data-ttu-id="e91c2-110">:::no-loc(Razor)::: Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-110">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e91c2-111">Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="e91c2-112">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `:::no-loc(Razor):::PagesMovieContext` :</span><span class="sxs-lookup"><span data-stu-id="e91c2-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e91c2-113">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e91c2-114">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-115">Sayfasında :::no-loc(Razor)::: , `OnGetAsync` veya `OnGet` sayfanın durumunu başlatmak için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-115">On a :::no-loc(Razor)::: Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="e91c2-116">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e91c2-117">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , Return deyimleri kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="e91c2-118">Örneğin gizlilik sayfası:</span><span class="sxs-lookup"><span data-stu-id="e91c2-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="e91c2-119">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e91c2-120">Örneğin, *Pages/filmler/ :::no-loc(Create)::: . cshtml.cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e91c2-120">For example, the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="e91c2-121">*Pages/filmler/ :::no-loc(Index)::: . cshtml* :::no-loc(Razor)::: sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-121">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml)]

<span data-ttu-id="e91c2-122">:::no-loc(Razor)::: HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-122">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="e91c2-123">Bir `@` simgenin arkasından [ :::no-loc(Razor)::: ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir :::no-loc(Razor)::: biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-123">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="e91c2-124">@pageYönergesi</span><span class="sxs-lookup"><span data-stu-id="e91c2-124">The @page directive</span></span>

<span data-ttu-id="e91c2-125">`@page` :::no-loc(Razor)::: Yönergesi, dosyayı bir MVC eylemi yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-125">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e91c2-126">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-126">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="e91c2-127">`@page` ve `@model` belirli biçimlendirme örneklerine örnek olarak verilebilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-127">`@page` and `@model` are examples of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="e91c2-128">Daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-128">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e91c2-129">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="e91c2-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e91c2-130">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-130">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-131">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-132">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="e91c2-133">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="e91c2-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e91c2-135">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e91c2-136">Bu,,, veya boş olduğunda bir erişim ihlali olmadığı anlamına gelir `model` `model.Movie` `model.Movie[0]` `null` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="e91c2-137">Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="e91c2-138">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="e91c2-138">The layout page</span></span>

<span data-ttu-id="e91c2-139">**:::no-loc(Razor)::: Pagesfilmi** , **ana sayfası** ve **gizliliği** bağlayan menüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-139">Select the menu links **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="e91c2-140">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="e91c2-141">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e91c2-142">*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e91c2-143">[Düzen](xref:mvc/views/layout) ŞABLONLARı, HTML kapsayıcı düzeninin şu şekilde olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="e91c2-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="e91c2-144">Tek bir yerde belirtildi.</span><span class="sxs-lookup"><span data-stu-id="e91c2-144">Specified in one place.</span></span>
* <span data-ttu-id="e91c2-145">Sitede birden çok sayfada uygulandı.</span><span class="sxs-lookup"><span data-stu-id="e91c2-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="e91c2-146">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e91c2-147">`RenderBody` , sayfaya özgü tüm görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="e91c2-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e91c2-148">Örneğin, **Gizlilik** bağlantısını seçin ve *Sayfalar/gizlilik. cshtml* görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e91c2-149">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="e91c2-149">ViewData and layout</span></span>

<span data-ttu-id="e91c2-150">*Sayfalar/filmler/ :::no-loc(Index)::: . cshtml* dosyasından aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e91c2-150">Consider the following markup from the *Pages/Movies/:::no-loc(Index):::.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e91c2-151">Önceki vurgulanan biçimlendirme C# ' ye geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-151">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="e91c2-152">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e91c2-153">`PageModel`Temel sınıf, `ViewData` verileri bir görünüme geçirmek için kullanılabilecek bir Dictionary özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="e91c2-154">Nesneler, `ViewData` bir \* **anahtar değeri** _ düzeniyle kullanılarak sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-154">Objects are added to the `ViewData` dictionary using a \* **key value** _ pattern.</span></span> <span data-ttu-id="e91c2-155">Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e91c2-156">`Title`Özelliği, _Pages/Shared/_Layout. cshtml \* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="e91c2-157">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="e91c2-158">Satır `@*Markup removed for brevity.*@` bir :::no-loc(Razor)::: açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-158">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="e91c2-159">HTML yorumlarından farklı olarak `<!-- -->` , :::no-loc(Razor)::: açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="e91c2-159">Unlike HTML comments `<!-- -->`, :::no-loc(Razor)::: comments are not sent to the client.</span></span> <span data-ttu-id="e91c2-160">Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.</span><span class="sxs-lookup"><span data-stu-id="e91c2-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e91c2-161">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e91c2-161">Update the layout</span></span>

1. <span data-ttu-id="e91c2-162">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **:::no-loc(Razor)::: pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="e91c2-163">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="e91c2-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/:::no-loc(Index):::">:::no-loc(Razor):::PagesMovie</a>
   ```

1. <span data-ttu-id="e91c2-164">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/:::no-loc(Index):::">RpMovie</a>
   ```

   <span data-ttu-id="e91c2-165">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e91c2-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e91c2-166">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e91c2-167">`asp-page="/Movies/:::no-loc(Index):::"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-167">The `asp-page="/Movies/:::no-loc(Index):::"` Tag Helper attribute and value creates a link to the `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-168">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e91c2-169">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="e91c2-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="e91c2-170">**Rpmovie** bağlantısını seçerek değişiklikleri kaydedin ve uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="e91c2-171">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="e91c2-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="e91c2-172">**Home** , **rpmovie** , **:::no-loc(Create):::** , **Edit** ve Links 'i test edin **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="e91c2-172">Test the **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** , and **:::no-loc(Delete):::** links.</span></span> <span data-ttu-id="e91c2-173">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e91c2-174">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e91c2-175">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="e91c2-176">Ondalık virgülden ekleme hakkında yönergeler için bkz. [GitHub sorunu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e91c2-177">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="e91c2-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e91c2-178">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar :::no-loc(Razor)::: . *Pages*</span><span class="sxs-lookup"><span data-stu-id="e91c2-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="e91c2-179">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="e91c2-180">:::no-loc(Create):::Sayfa modeli</span><span class="sxs-lookup"><span data-stu-id="e91c2-180">The :::no-loc(Create)::: page model</span></span>

<span data-ttu-id="e91c2-181">*Pages/filmler/ :::no-loc(Create)::: . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-181">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e91c2-182">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e91c2-183">:::no-loc(Create):::Sayfada başlatılacak durum yok, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e91c2-183">The :::no-loc(Create)::: page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e91c2-184">Öğreticide daha sonra, `OnGet` başlatma durumuna bir örnek gösterilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="e91c2-185">`Page`Yöntemi `PageResult` *:::no-loc(Create)::: . cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e91c2-185">The `Page` method creates a `PageResult` object that renders the *:::no-loc(Create):::.cshtml* page.</span></span>

<span data-ttu-id="e91c2-186">`Movie`Özelliği, [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e91c2-187">Form, :::no-loc(Create)::: form değerlerini gönderdiğinde, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-187">When the :::no-loc(Create)::: form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e91c2-188">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="e91c2-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e91c2-189">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e91c2-190">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e91c2-191">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="e91c2-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e91c2-192">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e91c2-193">Model hatası yoksa:</span><span class="sxs-lookup"><span data-stu-id="e91c2-193">If there are no model errors:</span></span>

* <span data-ttu-id="e91c2-194">Veriler kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-194">The data is saved.</span></span>
* <span data-ttu-id="e91c2-195">Tarayıcı :::no-loc(Index)::: sayfaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-195">The browser is redirected to the :::no-loc(Index)::: page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="e91c2-196">:::no-loc(Create)::: :::no-loc(Razor)::: Sayfa</span><span class="sxs-lookup"><span data-stu-id="e91c2-196">The :::no-loc(Create)::: :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="e91c2-197">*Pages/filmler/ :::no-loc(Create)::: . cshtml* :::no-loc(Razor)::: sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-197">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="e91c2-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e91c2-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e91c2-199">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e91c2-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 View::: No-Loc (Oluştur):::. cshtml sayfası](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e91c2-201">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e91c2-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e91c2-202">Aşağıdaki etiket yardımcıları, önceki biçimlendirmede gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="e91c2-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="e91c2-203">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e91c2-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e91c2-204">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e91c2-205">Yapı iskelesi altyapısı, :::no-loc(Razor)::: modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e91c2-205">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml?range=15-20)]

<span data-ttu-id="e91c2-206">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e91c2-207">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e91c2-208">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="e91c2-209">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="e91c2-210">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e91c2-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e91c2-211">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e91c2-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e91c2-212">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e91c2-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e91c2-213">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e91c2-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e91c2-214">Bu öğreticide :::no-loc(Razor)::: , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-214">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="e91c2-215">Örneği [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-215">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="e91c2-216">:::no-loc(Create):::,, :::no-loc(Delete)::: Ayrıntıları ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="e91c2-216">The :::no-loc(Create):::, :::no-loc(Delete):::, Details, and Edit pages</span></span>

<span data-ttu-id="e91c2-217">*Pages/filmler/ :::no-loc(Index)::: . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-217">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml.cs)]

<span data-ttu-id="e91c2-218">:::no-loc(Razor)::: Sayfalar öğesinden türetilir `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-218">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e91c2-219">Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-219">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="e91c2-220">Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `:::no-loc(Razor):::PagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-220">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="e91c2-221">Yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-221">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="e91c2-222">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-222">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e91c2-223">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-223">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-224">`OnGetAsync` ya da sayfa `OnGet` :::no-loc(Razor)::: için durumu başlatmak üzere bir sayfada çağırılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-224">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="e91c2-225">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-225">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e91c2-226">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , return yöntemi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-226">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="e91c2-227">Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-227">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e91c2-228">Örneğin, *Pages/filmler/ :::no-loc(Create)::: . cshtml.cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e91c2-228">For example, the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="e91c2-229">*Pages/filmler/ :::no-loc(Index)::: . cshtml* :::no-loc(Razor)::: sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-229">Examine the *Pages/Movies/:::no-loc(Index):::.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml)]

<span data-ttu-id="e91c2-230">:::no-loc(Razor)::: HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-230">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="e91c2-231">Bir `@` simgenin arkasından [ :::no-loc(Razor)::: ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir :::no-loc(Razor)::: biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-231">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="e91c2-232">`@page` :::no-loc(Razor)::: Yönergesi, dosyayı bir MVC eylemine yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-232">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e91c2-233">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-233">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="e91c2-234">`@page` , belirli bir biçimlendirmeyi geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-234">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="e91c2-235">Daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sözdizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-235">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e91c2-236">@modelYönergesi</span><span class="sxs-lookup"><span data-stu-id="e91c2-236">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e91c2-237">`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-237">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-238">Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-238">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-239">Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-239">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="e91c2-240">HTML Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="e91c2-240">HTML Helpers</span></span>

<span data-ttu-id="e91c2-241">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-241">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="e91c2-242"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-242">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e91c2-243">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-243">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e91c2-244">Diğer bir deyişle `model` ,, `model.Movie` , veya boş olduğunda erişim ihlali `model.Movie[0]` yoktur `null` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-244">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="e91c2-245">Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-245">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="e91c2-246">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="e91c2-246">The layout page</span></span>

<span data-ttu-id="e91c2-247">**:::no-loc(Razor)::: Pagesfilmi** , **ana sayfası** ve **gizliliği** bağlayan menüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-247">Select the menu links **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="e91c2-248">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-248">Each page shows the same menu layout.</span></span> <span data-ttu-id="e91c2-249">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-249">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e91c2-250">*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-250">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e91c2-251">[Düzen](xref:mvc/views/layout) şablonları, BIR sitenin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitede birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-251">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="e91c2-252">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-252">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e91c2-253">`RenderBody` , oluşturduğunuz tüm sayfaya özgü görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="e91c2-253">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e91c2-254">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Sayfa/Gizlilik. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-254">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e91c2-255">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="e91c2-255">ViewData and layout</span></span>

<span data-ttu-id="e91c2-256">*Sayfalar/filmler/ :::no-loc(Index)::: . cshtml* dosyasından aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e91c2-256">Consider the following code from the *Pages/Movies/:::no-loc(Index):::.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e91c2-257">Önceki vurgulanan kod C# ' ye geçme örneğidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-257">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="e91c2-258">`{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-258">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e91c2-259">`PageModel`Temel sınıfın, `ViewData` bir görünüme geçirmek istediğiniz verileri eklemek için kullanılabilecek bir Dictionary özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-259">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="e91c2-260">`ViewData`Bir anahtar/değer örüntüsünün kullanıldığı sözlüğe nesneler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-260">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="e91c2-261">Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-261">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e91c2-262">`Title`Özelliği *Pages/Shared/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-262">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e91c2-263">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-263">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="e91c2-264">Satır `@*Markup removed for brevity.*@` bir :::no-loc(Razor)::: açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-264">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="e91c2-265">HTML yorumlarından farklı olarak `<!-- -->` , :::no-loc(Razor)::: açıklamalar istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="e91c2-265">Unlike HTML comments `<!-- -->`, :::no-loc(Razor)::: comments are not sent to the client.</span></span> <span data-ttu-id="e91c2-266">Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.</span><span class="sxs-lookup"><span data-stu-id="e91c2-266">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e91c2-267">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e91c2-267">Update the layout</span></span>

<span data-ttu-id="e91c2-268">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **:::no-loc(Razor)::: pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-268">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="e91c2-269">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="e91c2-269">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/:::no-loc(Index):::">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="e91c2-270">Önceki öğeyi aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-270">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/:::no-loc(Index):::">RpMovie</a>
```

<span data-ttu-id="e91c2-271">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e91c2-271">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e91c2-272">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-272">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e91c2-273">`asp-page="/Movies/:::no-loc(Index):::"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="e91c2-273">The `asp-page="/Movies/:::no-loc(Index):::"` Tag Helper attribute and value creates a link to the `/Movies/:::no-loc(Index):::` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="e91c2-274">`asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-274">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e91c2-275">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="e91c2-275">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="e91c2-276">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="e91c2-276">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="e91c2-277">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="e91c2-277">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="e91c2-278">Diğer bağlantıları test edin ( **giriş** , **rpmovie** , **:::no-loc(Create):::** , **düzenleme** ve **:::no-loc(Delete):::** ).</span><span class="sxs-lookup"><span data-stu-id="e91c2-278">Test the other links ( **Home** , **RpMovie** , **:::no-loc(Create):::** , **Edit** , and **:::no-loc(Delete):::** ).</span></span> <span data-ttu-id="e91c2-279">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-279">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e91c2-280">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-280">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e91c2-281">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-281">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="e91c2-282">Bu GitHub, ondalık virgülden ekleme hakkında yönergeler için [4076 sorun](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-282">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e91c2-283">`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="e91c2-283">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e91c2-284">Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar :::no-loc(Razor)::: . *Pages*</span><span class="sxs-lookup"><span data-stu-id="e91c2-284">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="e91c2-285">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="e91c2-285">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="e91c2-286">:::no-loc(Create):::Sayfa modeli</span><span class="sxs-lookup"><span data-stu-id="e91c2-286">The :::no-loc(Create)::: page model</span></span>

<span data-ttu-id="e91c2-287">*Pages/filmler/ :::no-loc(Create)::: . cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-287">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e91c2-288">`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-288">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e91c2-289">:::no-loc(Create):::Sayfada başlatılacak durum yok, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e91c2-289">The :::no-loc(Create)::: page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e91c2-290">Öğreticide daha sonra `OnGet` Yöntem başlatma durumunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="e91c2-290">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="e91c2-291">`Page`Yöntemi `PageResult` *:::no-loc(Create)::: . cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e91c2-291">The `Page` method creates a `PageResult` object that renders the *:::no-loc(Create):::.cshtml* page.</span></span>

<span data-ttu-id="e91c2-292">`Movie`Özelliği, <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]] özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-292">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e91c2-293">Form, :::no-loc(Create)::: form değerlerini gönderdiğinde, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="e91c2-293">When the :::no-loc(Create)::: form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e91c2-294">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="e91c2-294">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e91c2-295">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-295">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e91c2-296">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-296">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e91c2-297">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="e91c2-297">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e91c2-298">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-298">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e91c2-299">Model hatası yoksa, veriler kaydedilir ve tarayıcı :::no-loc(Index)::: sayfaya yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-299">If there are no model errors, the data is saved, and the browser is redirected to the :::no-loc(Index)::: page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="e91c2-300">:::no-loc(Create)::: :::no-loc(Razor)::: Sayfa</span><span class="sxs-lookup"><span data-stu-id="e91c2-300">The :::no-loc(Create)::: :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="e91c2-301">*Pages/filmler/ :::no-loc(Create)::: . cshtml* :::no-loc(Razor)::: sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e91c2-301">Examine the *Pages/Movies/:::no-loc(Create):::.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="e91c2-302">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e91c2-302">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e91c2-303">Visual Studio etiketi, etiket `<form method="post">` yardımcıları için kullanılan farklı bir kalın yazı tipiyle görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e91c2-303">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 View::: No-Loc (Oluştur):::. cshtml sayfası](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e91c2-305">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e91c2-305">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e91c2-306">Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e91c2-306">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e91c2-307">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e91c2-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e91c2-308">Mac için Visual Studio etiket `<form method="post">` yardımcıları için kullanılan farklı kalın yazı tipinde etiketi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-308">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="e91c2-309">`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e91c2-309">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e91c2-310">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-310">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e91c2-311">Yapı iskelesi altyapısı, :::no-loc(Razor)::: modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e91c2-311">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml?range=15-20)]

<span data-ttu-id="e91c2-312">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e91c2-312">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e91c2-313">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e91c2-313">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e91c2-314">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .</span><span class="sxs-lookup"><span data-stu-id="e91c2-314">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="e91c2-315">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="e91c2-315">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e91c2-316">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e91c2-316">Additional resources</span></span>

* [<span data-ttu-id="e91c2-317">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="e91c2-317">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="e91c2-318">[Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e91c2-318">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
