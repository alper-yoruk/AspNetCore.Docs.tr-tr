---
title: ASP.NET Core yapı iskelesi olan Razor sayfalar
author: rick-anderson
description: Yapı iskelesi tarafından oluşturulan Razor sayfaları açıklar.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 22afbc729cc73427b3d04bee379534cda38b39bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774853"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="ddc4e-103">ASP.NET Core Razor Pages scafkatlama</span><span class="sxs-lookup"><span data-stu-id="ddc4e-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ddc4e-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ddc4e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ddc4e-105">Bu öğreticide, [önceki öğreticide](xref:tutorials/razor-pages/model)scafkatlama tarafından oluşturulan Razor Pages incelenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ddc4e-106">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="ddc4e-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ddc4e-107">*Pages/filmler/Index. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ddc4e-108">Razor Pages, öğesinden `PageModel`türetilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ddc4e-109">Kural gereği, `PageModel`-türetilmiş sınıfı çağrılır `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="ddc4e-110">Oluşturucu, `RazorPagesMovieContext` sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="ddc4e-111">Tüm yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="ddc4e-112">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ddc4e-113">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi Razor sayfasına bir film listesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ddc4e-114">`OnGetAsync`ya `OnGet` da sayfanın durumunu başlatmak için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="ddc4e-115">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ddc4e-116">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde`Task`, Return deyimleri kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="ddc4e-117">Dönüş türü `IActionResult` veya `Task<IActionResult>`olduğunda, bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ddc4e-118">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="ddc4e-119">*Pages/filmler/Index. cshtml* Razor sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ddc4e-120">Razor, HTML 'den C# 'e veya Razor 'e özgü biçimlendirmeye geçiş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ddc4e-121">Bir `@` sembol sonrasında [Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)olduğunda, bu, Razor 'e özgü işaretlere geçiş yapar, aksi halde C# dilinde geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="ddc4e-122">@page Yönergesi</span><span class="sxs-lookup"><span data-stu-id="ddc4e-122">The @page directive</span></span>

<span data-ttu-id="ddc4e-123">`@page` Razor yönergesi, dosyayı bir MVC eylemi yapar, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ddc4e-124">`@page`sayfada ilk Razor yönergesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ddc4e-125">`@page`, Razor 'e özgü biçimlendirmeye geçme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ddc4e-126">Daha fazla bilgi için bkz. [Razor söz dizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="ddc4e-127">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="ddc4e-128">`DisplayNameFor` HTML Yardımcısı, görünen adı `Title` belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ddc4e-129">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ddc4e-130">`model`Bu,,, veya `model.Movie` `model.Movie[0]` `null` boş olduğunda bir erişim ihlali olmadığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="ddc4e-131">Lambda ifadesi değerlendirildiğinde (örneğin, ile `@Html.DisplayFor(modelItem => item.Title)`), modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ddc4e-132">@model Yönergesi</span><span class="sxs-lookup"><span data-stu-id="ddc4e-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ddc4e-133">`@model` Yönerge, Razor sayfasına geçirilen modelin türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ddc4e-134">Yukarıdaki örnekte, `@model` satır türetilen sınıfı Razor sayfası için `PageModel`kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ddc4e-135">Model, sayfadaki `@Html.DisplayNameFor` ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="ddc4e-136">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="ddc4e-136">The layout page</span></span>

<span data-ttu-id="ddc4e-137">Menü bağlantılarını (**RazorPagesMovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="ddc4e-138">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="ddc4e-139">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ddc4e-140">*Pages/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ddc4e-141">[Düzen](xref:mvc/views/layout) ŞABLONLARı, HTML kapsayıcı düzeninin şu şekilde olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="ddc4e-142">Tek bir yerde belirtildi.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-142">Specified in one place.</span></span>
* <span data-ttu-id="ddc4e-143">Sitede birden çok sayfada uygulandı.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="ddc4e-144">`@RenderBody()` Satırı bulun.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ddc4e-145">`RenderBody`, sayfaya özgü tüm görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ddc4e-146">Örneğin, **Gizlilik** bağlantısını seçin ve *Sayfalar/gizlilik. cshtml* görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ddc4e-147">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="ddc4e-147">ViewData and layout</span></span>

<span data-ttu-id="ddc4e-148">*Pages/filmler/Index. cshtml* dosyasından aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ddc4e-149">Önceki vurgulanan biçimlendirme, C# ' ye geçiş yapan bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ddc4e-150">`{` Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ddc4e-151">Temel `PageModel` sınıf, verileri bir `ViewData` görünüme geçirmek için kullanılabilecek bir Dictionary özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="ddc4e-152">Nesneler, `ViewData` anahtara/değer düzeniyle kullanılarak sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="ddc4e-153">Yukarıdaki örnekte, `"Title"` özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ddc4e-154">`"Title"` Özelliği *Pages/Shared/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ddc4e-155">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="ddc4e-156">Satır `@*Markup removed for brevity.*@` bir Razor açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="ddc4e-157">HTML yorumlarının (`<!-- -->`) aksine, Razor açıklamaları istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ddc4e-158">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ddc4e-158">Update the layout</span></span>

<span data-ttu-id="ddc4e-159">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="ddc4e-160">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="ddc4e-161">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="ddc4e-162">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ddc4e-163">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ddc4e-164">`asp-page="/Movies/Index"` Etiket Yardımcısı özniteliği ve değeri `/Movies/Index` Razor sayfasına bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ddc4e-165">`asp-area` Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ddc4e-166">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="ddc4e-167">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="ddc4e-168">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="ddc4e-169">Diğer bağlantıları test edin (**giriş**, **rpmovie**, **oluşturma**, **düzenleme**ve **silme**).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="ddc4e-170">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ddc4e-171">`Price` Alana ondalık virgüller giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ddc4e-172">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanızı globalize için adımlar uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="ddc4e-173">Ondalık virgülden ekleme hakkında yönergeler için bkz. [GitHub sorunu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ddc4e-174">`Layout` Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ddc4e-175">Yukarıdaki biçimlendirme düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ddc4e-176">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ddc4e-177">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="ddc4e-177">The Create page model</span></span>

<span data-ttu-id="ddc4e-178">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ddc4e-179">`OnGet` Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ddc4e-180">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ddc4e-181">Öğreticide daha sonra, `OnGet` başlatma durumuna bir örnek gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="ddc4e-182">`Page` Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ddc4e-183">`Movie` Özelliği, [model bağlamayı](xref:mvc/models/model-binding)kabul etmek için `[BindProperty]` özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ddc4e-184">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ddc4e-185">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ddc4e-186">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ddc4e-187">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ddc4e-188">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ddc4e-189">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ddc4e-190">Model hatası yoksa, veriler kaydedilir ve tarayıcı dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="ddc4e-191">Razor Oluştur sayfası</span><span class="sxs-lookup"><span data-stu-id="ddc4e-191">The Create Razor Page</span></span>

<span data-ttu-id="ddc4e-192">*Pages/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="ddc4e-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddc4e-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ddc4e-194">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create. cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddc4e-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddc4e-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ddc4e-197">Aşağıdaki etiket yardımcıları, önceki biçimlendirmede gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddc4e-198">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddc4e-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ddc4e-199">Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="ddc4e-200">`<form method="post">` Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ddc4e-201">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ddc4e-202">Yapı iskelesi altyapısı, modeldeki her alan için (KIMLIK hariç), aşağıdakine benzer Razor biçimlendirmesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ddc4e-203">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` ve `<span asp-validation-for`) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ddc4e-204">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ddc4e-205">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`), `for` `Title` özelliğin etiket açıklamalı alt yazısını ve özniteliğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="ddc4e-206">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="ddc4e-207">Gibi etiket yardımcıları `<form method="post">`hakkında daha fazla bilgi için bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ddc4e-208">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ddc4e-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ddc4e-209">[Önceki: bir model](xref:tutorials/razor-pages/model)
> ekleme[Sonraki: veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ddc4e-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ddc4e-210">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ddc4e-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ddc4e-211">Bu öğreticide, [önceki öğreticide](xref:tutorials/razor-pages/model)scafkatlama tarafından oluşturulan Razor Pages incelenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="ddc4e-212">Örneği [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ddc4e-213">Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları</span><span class="sxs-lookup"><span data-stu-id="ddc4e-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ddc4e-214">*Pages/filmler/Index. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ddc4e-215">Razor Pages, öğesinden `PageModel`türetilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ddc4e-216">Kural gereği, `PageModel`-türetilmiş sınıfı çağrılır `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="ddc4e-217">Oluşturucu, `RazorPagesMovieContext` sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="ddc4e-218">Tüm yapı iskelesi sayfaları bu düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="ddc4e-219">Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ddc4e-220">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi Razor sayfasına bir film listesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ddc4e-221">`OnGetAsync`ya `OnGet` da bir Razor sayfasında, sayfanın durumunu başlatmak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="ddc4e-222">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ddc4e-223">Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde`Task`, return yöntemi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="ddc4e-224">Dönüş türü `IActionResult` veya `Task<IActionResult>`olduğunda, bir return ifadesinin sağlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ddc4e-225">Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="ddc4e-226">*Pages/filmler/Index. cshtml* Razor sayfasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ddc4e-227">Razor, HTML 'den C# 'e veya Razor 'e özgü biçimlendirmeye geçiş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ddc4e-228">Bir `@` sembol sonrasında [Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)olduğunda, bu, Razor 'e özgü işaretlere geçiş yapar, aksi halde C# dilinde geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="ddc4e-229">`@page` Razor yönergesi, dosyayı bir MVC eylemine dönüştürür, bu da istekleri işleyebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ddc4e-230">`@page`sayfada ilk Razor yönergesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ddc4e-231">`@page`, Razor 'e özgü biçimlendirmeye geçme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ddc4e-232">Daha fazla bilgi için bkz. [Razor söz dizimi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="ddc4e-233">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="ddc4e-234">`DisplayNameFor` HTML Yardımcısı, görünen adı `Title` belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ddc4e-235">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ddc4e-236">Diğer `model`bir deyişle, `model.Movie`,, veya `model.Movie[0]` `null` boş olduğunda erişim ihlali yoktur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="ddc4e-237">Lambda ifadesi değerlendirildiğinde (örneğin, ile `@Html.DisplayFor(modelItem => item.Title)`), modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ddc4e-238">@model Yönergesi</span><span class="sxs-lookup"><span data-stu-id="ddc4e-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ddc4e-239">`@model` Yönerge, Razor sayfasına geçirilen modelin türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ddc4e-240">Yukarıdaki örnekte, `@model` satır türetilen sınıfı Razor sayfası için `PageModel`kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ddc4e-241">Model, sayfadaki `@Html.DisplayNameFor` ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="ddc4e-242">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="ddc4e-242">The layout page</span></span>

<span data-ttu-id="ddc4e-243">Menü bağlantılarını (**RazorPagesMovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="ddc4e-244">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="ddc4e-245">Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ddc4e-246">*Pages/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ddc4e-247">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="ddc4e-248">`@RenderBody()` Satırı bulun.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ddc4e-249">`RenderBody`, oluşturduğunuz tüm sayfaya özgü görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ddc4e-250">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Sayfa/Gizlilik. cshtml** görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ddc4e-251">ViewData ve Layout</span><span class="sxs-lookup"><span data-stu-id="ddc4e-251">ViewData and layout</span></span>

<span data-ttu-id="ddc4e-252">*Pages/filmler/Index. cshtml* dosyasından aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ddc4e-253">Yukarıdaki vurgulanmış kod, C# ' de Razor geçişi örneğidir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ddc4e-254">`{` Ve `}` karakterleri bir C# kodu bloğunu kapsar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ddc4e-255">`PageModel` Temel sınıfın, bir görünüme `ViewData` geçirmek istediğiniz verileri eklemek için kullanılabilecek bir Dictionary özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="ddc4e-256">Bir anahtar/değer örüntüsünün kullanıldığı `ViewData` sözlüğe nesneler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="ddc4e-257">Yukarıdaki örnekte, "title" özelliği `ViewData` sözlüğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ddc4e-258">"Title" özelliği *sayfa/paylaşılan/_Layout. cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ddc4e-259">Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="ddc4e-260">Satır `@*Markup removed for brevity.*@` , düzen dosyanızda görünmeyen bir Razor açıklamadır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="ddc4e-261">HTML yorumlarının (`<!-- -->`) aksine, Razor açıklamaları istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ddc4e-262">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ddc4e-262">Update the layout</span></span>

<span data-ttu-id="ddc4e-263">`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **filmi** görüntüleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="ddc4e-264">*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="ddc4e-265">Önceki öğeyi aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="ddc4e-266">Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ddc4e-267">Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ddc4e-268">`asp-page="/Movies/Index"` Etiket Yardımcısı özniteliği ve değeri `/Movies/Index` Razor sayfasına bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ddc4e-269">`asp-area` Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ddc4e-270">Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="ddc4e-271">Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="ddc4e-272">Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="ddc4e-273">Diğer bağlantıları test edin (**giriş**, **rpmovie**, **oluşturma**, **düzenleme**ve **silme**).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="ddc4e-274">Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ddc4e-275">`Price` Alana ondalık virgüller giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ddc4e-276">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanızı globalize için adımlar uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="ddc4e-277">Bu GitHub, ondalık virgülden ekleme hakkında yönergeler için [4076 sorun](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ddc4e-278">`Layout` Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ddc4e-279">Yukarıdaki biçimlendirme düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ddc4e-280">Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="ddc4e-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ddc4e-281">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="ddc4e-281">The Create page model</span></span>

<span data-ttu-id="ddc4e-282">*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ddc4e-283">`OnGet` Yöntemi, sayfa için gereken tüm durumları başlatır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ddc4e-284">Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ddc4e-285">Öğreticide daha sonra Yöntem başlatma `OnGet` durumunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="ddc4e-286">`Page` Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ddc4e-287">`Movie` Özelliği, [model bağlamayı](xref:mvc/models/model-binding)kabul etmek için `[BindProperty]` özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ddc4e-288">Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ddc4e-289">Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ddc4e-290">Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ddc4e-291">Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ddc4e-292">Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ddc4e-293">İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ddc4e-294">Model hatası yoksa, veriler kaydedilir ve tarayıcı dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="ddc4e-295">Razor Oluştur sayfası</span><span class="sxs-lookup"><span data-stu-id="ddc4e-295">The Create Razor Page</span></span>

<span data-ttu-id="ddc4e-296">*Pages/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="ddc4e-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddc4e-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ddc4e-298">Visual Studio etiketi, `<form method="post">` etiket yardımcıları için kullanılan farklı bir kalın yazı tipiyle görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create. cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddc4e-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddc4e-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ddc4e-301">Gibi etiket yardımcıları `<form method="post">`hakkında daha fazla bilgi için bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddc4e-302">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddc4e-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ddc4e-303">Mac için Visual Studio etiket yardımcıları `<form method="post">` için kullanılan farklı kalın yazı tipinde etiketi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="ddc4e-304">`<form method="post">` Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ddc4e-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ddc4e-305">Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ddc4e-306">Yapı iskelesi altyapısı, modeldeki Razor her bir alan IÇIN (kimlik dışında) aşağıdakine benzer bir biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ddc4e-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ddc4e-307">[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` ve `<span asp-validation-for`) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ddc4e-308">Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ddc4e-309">Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`), `for` `Title` özelliğin etiket açıklamalı alt yazısını ve özniteliğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="ddc4e-310">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="ddc4e-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ddc4e-311">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ddc4e-311">Additional resources</span></span>

* [<span data-ttu-id="ddc4e-312">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="ddc4e-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="ddc4e-313">[Önceki: bir model](xref:tutorials/razor-pages/model)
> ekleme[Sonraki: veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ddc4e-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
