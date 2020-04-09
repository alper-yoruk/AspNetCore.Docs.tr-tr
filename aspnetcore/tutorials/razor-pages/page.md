---
title: ASP.NET Core İskeleli Jilet Sayfaları
author: rick-anderson
description: İskele tarafından oluşturulan Jilet Sayfaları açıklar.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: cec4295a2c08c89db0975808583f41c7d09bfc88
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662451"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="808a2-103">ASP.NET Core İskeleli Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="808a2-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="808a2-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="808a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="808a2-105">Bu öğretici, [önceki öğreticide](xref:tutorials/razor-pages/model)iskele tarafından oluşturulan Razor Pages inceler.</span><span class="sxs-lookup"><span data-stu-id="808a2-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="808a2-106">Sayfaları Oluştur, Sil, Ayrıntı ve Düzenle</span><span class="sxs-lookup"><span data-stu-id="808a2-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="808a2-107">*Sayfaları/Filmleri/Index.cshtml.cs* Sayfa Modelini Inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="808a2-108">Jilet Sayfaları `PageModel`türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="808a2-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="808a2-109">Kural olarak, `PageModel`-türetilmiş `<PageName>Model`sınıf denir.</span><span class="sxs-lookup"><span data-stu-id="808a2-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="808a2-110">Oluşturucu sayfaya eklemek için `RazorPagesMovieContext` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) kullanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="808a2-111">Tüm iskele sayfaları bu deseni takip eder.</span><span class="sxs-lookup"><span data-stu-id="808a2-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="808a2-112">Entity Framework ile asynchronous programlama hakkında daha fazla bilgi için [Asynchronous koduna](xref:data/ef-rp/intro#asynchronous-code) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="808a2-113">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntem bir film listesini Razor Page'e döndürür.</span><span class="sxs-lookup"><span data-stu-id="808a2-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="808a2-114">`OnGetAsync`veya `OnGet` sayfanın durumunu başlatmaya çağrılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="808a2-115">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="808a2-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="808a2-116">İade `OnGet` `void` veya `OnGetAsync` `Task`rirken, iade deyimi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="808a2-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="808a2-117">İade türü `IActionResult` veya `Task<IActionResult>`, bir iade deyimi sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="808a2-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="808a2-118">Örneğin, *Sayfalar/Filmler/Create.cshtml.cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="808a2-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="808a2-119">*Sayfaları/Filmleri/Index.cshtml* Jilet Sayfasını Inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="808a2-120">Razor HTML'den C#'a veya Jilete özgü biçimlendirmeye geçiş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="808a2-121">Bir `@` [sembol, Jilet ayrılmış](xref:mvc/views/razor#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür, aksi takdirde C#'a dönüşür.</span><span class="sxs-lookup"><span data-stu-id="808a2-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="808a2-122">Direktif @page</span><span class="sxs-lookup"><span data-stu-id="808a2-122">The @page directive</span></span>

<span data-ttu-id="808a2-123">`@page` Razor yönergesi dosyayı bir MVC eylemi yapar, bu da istekleri işleyebilir anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="808a2-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="808a2-124">`@page`bir sayfadaki ilk Razor direktifi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="808a2-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="808a2-125">`@page`Jilete özgü biçimlendirmeye geçişin bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="808a2-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="808a2-126">Daha fazla bilgi için [Razor sözdizimine](xref:mvc/views/razor#razor-syntax) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="808a2-127">Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="808a2-128">HTML `DisplayNameFor` Yardımcısı, görüntü `Title` adını belirlemek için lambda ifadesinde başvurulan özelliği denetler.</span><span class="sxs-lookup"><span data-stu-id="808a2-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="808a2-129">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="808a2-130">Bu, `model`, , , `model.Movie`veya `model.Movie[0]` boş `null` olduğunda hiçbir erişim ihlali anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="808a2-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="808a2-131">Lambda ifadesi değerlendirildiğinde (örneğin, modelin özellik değerleri `@Html.DisplayFor(modelItem => item.Title)`ile) değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="808a2-132">Direktif @model</span><span class="sxs-lookup"><span data-stu-id="808a2-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="808a2-133">Yönerge, `@model` Razor Page'e geçirilen modelin türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="808a2-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="808a2-134">Önceki örnekte, `@model` satır `PageModel`-türetilmiş sınıfı Razor Page için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="808a2-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="808a2-135">Model, sayfadaki `@Html.DisplayNameFor` HTML `@Html.DisplayFor` Yardımcıları ve [HTML Yardımcıları'nda](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="808a2-136">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="808a2-136">The layout page</span></span>

<span data-ttu-id="808a2-137">Menü bağlantılarını seçin **(RazorPagesMovie,** **Home**, ve **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="808a2-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="808a2-138">Her sayfa aynı menü düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="808a2-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="808a2-139">Menü düzeni *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="808a2-140">*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="808a2-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="808a2-141">[Düzen](xref:mvc/views/layout) şablonları HTML kapsayıcı düzeninin aşağıdakileri olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="808a2-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="808a2-142">Tek bir yerde belirtilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-142">Specified in one place.</span></span>
* <span data-ttu-id="808a2-143">Sitede birden çok sayfada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="808a2-144">`@RenderBody()` Hattı bul.</span><span class="sxs-lookup"><span data-stu-id="808a2-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="808a2-145">`RenderBody`sayfaya özel tüm görünümlerin bulunduğu ve düzen sayfasına *sarılmış* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="808a2-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="808a2-146">Örneğin, **Gizlilik** bağlantısını seçin ve *Pages/Privacy.cshtml* görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="808a2-147">ViewData ve düzen</span><span class="sxs-lookup"><span data-stu-id="808a2-147">ViewData and layout</span></span>

<span data-ttu-id="808a2-148">*Sayfalar/Filmler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="808a2-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="808a2-149">Önceki vurgulanan biçimlendirme, Razor'Un C#'a geçişine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="808a2-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="808a2-150">`{` Ve `}` karakterler C# kodu bloğunu içine alar.</span><span class="sxs-lookup"><span data-stu-id="808a2-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="808a2-151">Taban `PageModel` sınıf, `ViewData` verileri Görünüm'e geçirmek için kullanılabilecek bir sözlük özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="808a2-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="808a2-152">Nesneler anahtar/değer `ViewData` deseni kullanılarak sözlüklere eklenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="808a2-153">Önceki örnekte, `"Title"` özellik sözlük eklenir. `ViewData`</span><span class="sxs-lookup"><span data-stu-id="808a2-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="808a2-154">`"Title"` Özellik, *Pages/Shared/_Layout.cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="808a2-155">Aşağıdaki biçimlendirme *_Layout.cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="808a2-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="808a2-156">Satır `@*Markup removed for brevity.*@` bir Razor yorumdur.</span><span class="sxs-lookup"><span data-stu-id="808a2-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="808a2-157">HTML yorumlarının`<!-- -->`aksine ( ), Razor yorumları istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="808a2-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="808a2-158">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="808a2-158">Update the layout</span></span>

<span data-ttu-id="808a2-159">`<title>` *Sayfa/Paylaşılan/_Layout.cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **Film** görüntülemek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="808a2-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="808a2-160">*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında aşağıdaki bağlantı öğesini bulun.</span><span class="sxs-lookup"><span data-stu-id="808a2-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="808a2-161">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="808a2-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="808a2-162">Önceki bağlantı öğesi bir [Etiket Yardımcısı'dır.](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="808a2-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="808a2-163">Bu durumda, [Çapa Etiketi Yardımcısı.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="808a2-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="808a2-164">`asp-page="/Movies/Index"` Tag Helper özniteliği ve `/Movies/Index` değeri, Razor Page'e bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="808a2-165">Öznitelik `asp-area` değeri boşolduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="808a2-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="808a2-166">Daha fazla bilgi için [Alanlar'a](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="808a2-167">Değişikliklerinizi kaydedin ve **RpMovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="808a2-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="808a2-168">Herhangi bir sorun varsa GitHub [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="808a2-169">Diğer bağlantıları test edin **(Ana Sayfa**, **RpMovie**, **Oluştur**, **Düzenle**ve **Sil**).</span><span class="sxs-lookup"><span data-stu-id="808a2-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="808a2-170">Her sayfa, tarayıcı sekmesinde görebileceğiniz başlığı ayarlar. Bir sayfayı yer imi yaptığınızda, başlık yer imi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="808a2-171">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="808a2-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="808a2-172">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halklar ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için, uygulamanızı küreselleştirmek için adımlar atmalısınız.</span><span class="sxs-lookup"><span data-stu-id="808a2-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="808a2-173">Ondalık virgül ekleme yönergeleri için bu [GitHub sorunu 4076'ya](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="808a2-174">Özellik `Layout` *Pages/_ViewStart.cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="808a2-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="808a2-175">Önceki biçimlendirme, düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/Paylaşılan/_Layout.cshtml* olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="808a2-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="808a2-176">Daha fazla bilgi için [Düzen'e](xref:razor-pages/index#layout) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="808a2-177">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="808a2-177">The Create page model</span></span>

<span data-ttu-id="808a2-178">*Sayfalar/Filmler/Create.cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="808a2-179">Yöntem, `OnGet` sayfa için gereken durumu başlangıç olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="808a2-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="808a2-180">Create sayfasının başlatılması için herhangi bir durumu `Page` yoktur, bu nedenle döndürülür.</span><span class="sxs-lookup"><span data-stu-id="808a2-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="808a2-181">Daha sonra öğreticide, `OnGet` durum başlatma örneği gösterilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="808a2-182">Yöntem, `Page` `PageResult` *Create.cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="808a2-183">`Movie` Özellik, `[BindProperty]` [bağlamayı modellemek](xref:mvc/models/model-binding)için özniteliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="808a2-184">Form oluştur form değerlerini yayınladığında, ASP.NET Çekirdek çalışma süresi deftere `Movie` nakledilen değerleri modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="808a2-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="808a2-185">Yöntem, `OnPostAsync` sayfa gönderileri veri oluşturduğunda çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="808a2-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="808a2-186">Herhangi bir model hatası varsa, form, deftere nakledilen form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="808a2-187">Çoğu model hatası form deftere nakledilmeden önce istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="808a2-188">Model hatasıörneği, tarih alanı için tarihe dönüştürülemeyen bir değer deftere nakletmektir.</span><span class="sxs-lookup"><span data-stu-id="808a2-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="808a2-189">İstemci tarafı doğrulama ve model doğrulama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="808a2-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="808a2-190">Model hatası yoksa, veriler kaydedilir ve tarayıcı Dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="808a2-191">Jilet Oluştur Sayfası</span><span class="sxs-lookup"><span data-stu-id="808a2-191">The Create Razor Page</span></span>

<span data-ttu-id="808a2-192">*Sayfaları/Filmleri/Create.cshtml* Jilet Sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="808a2-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808a2-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="808a2-194">Visual Studio, Tag Helpers için kullanılan farklı bir kalın yazı tipinde aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="808a2-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="808a2-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808a2-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="808a2-197">Aşağıdaki Etiket Yardımcıları önceki biçimlendirmede gösterilir:</span><span class="sxs-lookup"><span data-stu-id="808a2-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="808a2-198">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808a2-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="808a2-199">Visual Studio, Tag Helpers için kullanılan farklı bir kalın yazı tipinde aşağıdaki etiketleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="808a2-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="808a2-200">Öğe `<form method="post">` bir [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="808a2-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="808a2-201">Form Tag Helper otomatik olarak bir [antiforgery belirteci](xref:security/anti-request-forgery)içerir.</span><span class="sxs-lookup"><span data-stu-id="808a2-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="808a2-202">İskele motoru, modeldeki her alan için (kimlik hariç) aşağıdakilere benzer jilet biçimlendirmeoluşturur:</span><span class="sxs-lookup"><span data-stu-id="808a2-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="808a2-203">[Doğrulama Etiketi Yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` `<span asp-validation-for`ve ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="808a2-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="808a2-204">Doğrulama daha sonra bu seride daha ayrıntılı olarak ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="808a2-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="808a2-205">[Etiket Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) `<label asp-for="Movie.Title" class="control-label"></label>`( ) `for` `Title` özelliği için etiket başlığı ve öznitelik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="808a2-206">[Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulama sı için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="808a2-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="808a2-207">Tag Helpers hakkında `<form method="post">`daha fazla bilgi için , [ASP.NET](xref:mvc/views/tag-helpers/intro)bkz.</span><span class="sxs-lookup"><span data-stu-id="808a2-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="808a2-208">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="808a2-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="808a2-209">[Önceki: Sonraki model](xref:tutorials/razor-pages/model)
> [ekleme: Veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="808a2-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="808a2-210">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="808a2-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="808a2-211">Bu öğretici, [önceki öğreticide](xref:tutorials/razor-pages/model)iskele tarafından oluşturulan Razor Pages inceler.</span><span class="sxs-lookup"><span data-stu-id="808a2-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="808a2-212">Örneği [görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)</span><span class="sxs-lookup"><span data-stu-id="808a2-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="808a2-213">Sayfaları Oluştur, Sil, Ayrıntı ve Düzenle</span><span class="sxs-lookup"><span data-stu-id="808a2-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="808a2-214">*Sayfaları/Filmleri/Index.cshtml.cs* Sayfa Modelini Inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="808a2-215">Jilet Sayfaları `PageModel`türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="808a2-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="808a2-216">Kural olarak, `PageModel`-türetilmiş `<PageName>Model`sınıf denir.</span><span class="sxs-lookup"><span data-stu-id="808a2-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="808a2-217">Oluşturucu sayfaya eklemek için `RazorPagesMovieContext` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) kullanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="808a2-218">Tüm iskele sayfaları bu deseni takip eder.</span><span class="sxs-lookup"><span data-stu-id="808a2-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="808a2-219">Entity Framework ile asynchronous programlama hakkında daha fazla bilgi için [Asynchronous koduna](xref:data/ef-rp/intro#asynchronous-code) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="808a2-220">Sayfa için bir istek yapıldığında, `OnGetAsync` yöntem bir film listesini Razor Page'e döndürür.</span><span class="sxs-lookup"><span data-stu-id="808a2-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="808a2-221">`OnGetAsync`veya `OnGet` sayfanın durumunu niçin başlatılması için Bir Jilet Sayfası'nda çağrılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="808a2-222">Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="808a2-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="808a2-223">İade `OnGet` `void` veya `OnGetAsync` `Task`rirken, iade yöntemi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="808a2-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="808a2-224">İade türü `IActionResult` veya `Task<IActionResult>`, bir iade deyimi sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="808a2-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="808a2-225">Örneğin, *Sayfalar/Filmler/Create.cshtml.cs* `OnPostAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="808a2-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="808a2-226">*Sayfaları/Filmleri/Index.cshtml* Jilet Sayfasını Inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="808a2-227">Razor HTML'den C#'a veya Jilete özgü biçimlendirmeye geçiş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="808a2-228">Bir `@` [sembol, Jilet ayrılmış](xref:mvc/views/razor#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür, aksi takdirde C#'a dönüşür.</span><span class="sxs-lookup"><span data-stu-id="808a2-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="808a2-229">`@page` Razor yönergesi, dosyayı bir MVC eylemine dönüştürür, bu da istekleri işleyebilir anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="808a2-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="808a2-230">`@page`bir sayfadaki ilk Razor direktifi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="808a2-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="808a2-231">`@page`Jilete özgü biçimlendirmeye geçişin bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="808a2-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="808a2-232">Daha fazla bilgi için [Razor sözdizimine](xref:mvc/views/razor#razor-syntax) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="808a2-233">Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="808a2-234">HTML `DisplayNameFor` Yardımcısı, görüntü `Title` adını belirlemek için lambda ifadesinde başvurulan özelliği denetler.</span><span class="sxs-lookup"><span data-stu-id="808a2-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="808a2-235">Lambda ifadesi değerlendirilmek yerine incelenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="808a2-236">Bu, `model`, , , `model.Movie`veya `model.Movie[0]` boş `null` olduğunda hiçbir erişim ihlali anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="808a2-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="808a2-237">Lambda ifadesi değerlendirildiğinde (örneğin, modelin özellik değerleri `@Html.DisplayFor(modelItem => item.Title)`ile) değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="808a2-238">Direktif @model</span><span class="sxs-lookup"><span data-stu-id="808a2-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="808a2-239">Yönerge, `@model` Razor Page'e geçirilen modelin türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="808a2-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="808a2-240">Önceki örnekte, `@model` satır `PageModel`-türetilmiş sınıfı Razor Page için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="808a2-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="808a2-241">Model, sayfadaki `@Html.DisplayNameFor` HTML `@Html.DisplayFor` Yardımcıları ve [HTML Yardımcıları'nda](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="808a2-242">Düzen sayfası</span><span class="sxs-lookup"><span data-stu-id="808a2-242">The layout page</span></span>

<span data-ttu-id="808a2-243">Menü bağlantılarını seçin **(RazorPagesMovie,** **Home**, ve **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="808a2-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="808a2-244">Her sayfa aynı menü düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="808a2-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="808a2-245">Menü düzeni *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="808a2-246">*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="808a2-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="808a2-247">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="808a2-248">`@RenderBody()` Hattı bul.</span><span class="sxs-lookup"><span data-stu-id="808a2-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="808a2-249">`RenderBody`oluşturduğunuz tüm sayfaya özgü görünümlerin düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="808a2-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="808a2-250">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Pages/Privacy.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="808a2-251">ViewData ve düzen</span><span class="sxs-lookup"><span data-stu-id="808a2-251">ViewData and layout</span></span>

<span data-ttu-id="808a2-252">*Sayfalar/Filmler/Index.cshtml* dosyasındaki aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="808a2-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="808a2-253">Önceki vurgulanan kod, Razor'Un C#'a geçişine bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="808a2-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="808a2-254">`{` Ve `}` karakterler C# kodu bloğunu içine alar.</span><span class="sxs-lookup"><span data-stu-id="808a2-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="808a2-255">Taban `PageModel` sınıfın, `ViewData` Görünüm'e geçirmek istediğiniz verileri eklemek için kullanılabilecek bir sözlük özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="808a2-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="808a2-256">Anahtar/değer deseni `ViewData` kullanarak sözlük içine nesneler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="808a2-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="808a2-257">Önceki örnekte, "Başlık" özelliği sözlük eklenir. `ViewData`</span><span class="sxs-lookup"><span data-stu-id="808a2-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="808a2-258">"Başlık" özelliği *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="808a2-259">Aşağıdaki biçimlendirme *_Layout.cshtml* dosyasının ilk birkaç satırını gösterir.</span><span class="sxs-lookup"><span data-stu-id="808a2-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="808a2-260">Satır, `@*Markup removed for brevity.*@` düzen dosyanızda görünmeyen bir Razor yorumudur.</span><span class="sxs-lookup"><span data-stu-id="808a2-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="808a2-261">HTML yorumlarının`<!-- -->`aksine ( ), Razor yorumları istemciye gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="808a2-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="808a2-262">Düzeni güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="808a2-262">Update the layout</span></span>

<span data-ttu-id="808a2-263">`<title>` *Sayfa/Paylaşılan/_Layout.cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **Film** görüntülemek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="808a2-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="808a2-264">*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında aşağıdaki bağlantı öğesini bulun.</span><span class="sxs-lookup"><span data-stu-id="808a2-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="808a2-265">Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="808a2-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="808a2-266">Önceki bağlantı öğesi bir [Etiket Yardımcısı'dır.](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="808a2-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="808a2-267">Bu durumda, [Çapa Etiketi Yardımcısı.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="808a2-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="808a2-268">`asp-page="/Movies/Index"` Tag Helper özniteliği ve `/Movies/Index` değeri, Razor Page'e bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="808a2-269">Öznitelik `asp-area` değeri boşolduğundan, alan bağlantıda kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="808a2-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="808a2-270">Daha fazla bilgi için [Alanlar'a](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="808a2-271">Değişikliklerinizi kaydedin ve **RpMovie** bağlantısına tıklayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="808a2-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="808a2-272">Herhangi bir sorun varsa GitHub [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="808a2-273">Diğer bağlantıları test edin **(Ana Sayfa**, **RpMovie**, **Oluştur**, **Düzenle**ve **Sil**).</span><span class="sxs-lookup"><span data-stu-id="808a2-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="808a2-274">Her sayfa, tarayıcı sekmesinde görebileceğiniz başlığı ayarlar. Bir sayfayı yer imi yaptığınızda, başlık yer imi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="808a2-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="808a2-275">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="808a2-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="808a2-276">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halklar ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için, uygulamanızı küreselleştirmek için adımlar atmalısınız.</span><span class="sxs-lookup"><span data-stu-id="808a2-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="808a2-277">Ondalık virgül ekleme yönergeleri için bu [GitHub sorunu 4076.](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)</span><span class="sxs-lookup"><span data-stu-id="808a2-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="808a2-278">Özellik `Layout` *Pages/_ViewStart.cshtml* dosyasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="808a2-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="808a2-279">Önceki biçimlendirme, düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/Paylaşılan/_Layout.cshtml* olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="808a2-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="808a2-280">Daha fazla bilgi için [Düzen'e](xref:razor-pages/index#layout) bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="808a2-281">Sayfa oluştur modeli</span><span class="sxs-lookup"><span data-stu-id="808a2-281">The Create page model</span></span>

<span data-ttu-id="808a2-282">*Sayfalar/Filmler/Create.cshtml.cs* sayfa modelini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="808a2-283">Yöntem, `OnGet` sayfa için gereken durumu başlangıç olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="808a2-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="808a2-284">Create sayfasının başlatılması için herhangi bir durumu `Page` yoktur, bu nedenle döndürülür.</span><span class="sxs-lookup"><span data-stu-id="808a2-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="808a2-285">Daha sonra öğretici `OnGet` de yöntem durum başlatma bakın.</span><span class="sxs-lookup"><span data-stu-id="808a2-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="808a2-286">Yöntem, `Page` `PageResult` *Create.cshtml* sayfasını işleyen bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="808a2-287">`Movie` Özellik, `[BindProperty]` [bağlamayı modellemek](xref:mvc/models/model-binding)için özniteliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="808a2-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="808a2-288">Form oluştur form değerlerini yayınladığında, ASP.NET Çekirdek çalışma süresi deftere `Movie` nakledilen değerleri modele bağlar.</span><span class="sxs-lookup"><span data-stu-id="808a2-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="808a2-289">Yöntem, `OnPostAsync` sayfa gönderileri veri oluşturduğunda çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="808a2-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="808a2-290">Herhangi bir model hatası varsa, form, deftere nakledilen form verileriyle birlikte yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="808a2-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="808a2-291">Çoğu model hatası form deftere nakledilmeden önce istemci tarafında yakalanabilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="808a2-292">Model hatasıörneği, tarih alanı için tarihe dönüştürülemeyen bir değer deftere nakletmektir.</span><span class="sxs-lookup"><span data-stu-id="808a2-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="808a2-293">İstemci tarafı doğrulama ve model doğrulama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="808a2-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="808a2-294">Model hatası yoksa, veriler kaydedilir ve tarayıcı Dizin sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="808a2-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="808a2-295">Jilet Oluştur Sayfası</span><span class="sxs-lookup"><span data-stu-id="808a2-295">The Create Razor Page</span></span>

<span data-ttu-id="808a2-296">*Sayfaları/Filmleri/Create.cshtml* Jilet Sayfa dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="808a2-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="808a2-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808a2-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="808a2-298">Visual Studio `<form method="post">` etiketi Tag Helpers için kullanılan farklı bir kalın yazı tipinde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="808a2-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="808a2-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808a2-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="808a2-301">Tag Helpers hakkında `<form method="post">`daha fazla bilgi için , [ASP.NET](xref:mvc/views/tag-helpers/intro)bkz.</span><span class="sxs-lookup"><span data-stu-id="808a2-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="808a2-302">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808a2-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="808a2-303">Mac için Visual `<form method="post">` Studio etiketi Tag Helpers için kullanılan farklı bir kalın yazı tipinde görüntüler.</span><span class="sxs-lookup"><span data-stu-id="808a2-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="808a2-304">Öğe `<form method="post">` bir [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="808a2-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="808a2-305">Form Tag Helper otomatik olarak bir [antiforgery belirteci](xref:security/anti-request-forgery)içerir.</span><span class="sxs-lookup"><span data-stu-id="808a2-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="808a2-306">İskele motoru, modeldeki her alan için (kimlik hariç) aşağıdakilere benzer jilet biçimlendirmeoluşturur:</span><span class="sxs-lookup"><span data-stu-id="808a2-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="808a2-307">[Doğrulama Etiketi Yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` `<span asp-validation-for`ve ) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="808a2-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="808a2-308">Doğrulama daha sonra bu seride daha ayrıntılı olarak ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="808a2-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="808a2-309">[Etiket Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) `<label asp-for="Movie.Title" class="control-label"></label>`( ) `for` `Title` özelliği için etiket başlığı ve öznitelik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="808a2-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="808a2-310">[Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulama sı için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="808a2-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="808a2-311">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="808a2-311">Additional resources</span></span>

* [<span data-ttu-id="808a2-312">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="808a2-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="808a2-313">[Önceki: Sonraki model](xref:tutorials/razor-pages/model)
> [ekleme: Veritabanı](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="808a2-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
