---
title: 5. kısım, oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: Sayfalardaki eğitim serisinin 5. bölümü Razor .
ms.author: riande
ms.date: 09/20/2020
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419973"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="3f299-103">5. bölüm, ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="3f299-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="3f299-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3f299-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f299-105">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="3f299-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="3f299-106">**ReleaseDate** iki sözcük, **Yayın tarihi** olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3f299-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Chrome 'da açık film uygulaması](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="3f299-108">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="3f299-108">Update the generated code</span></span>

<span data-ttu-id="3f299-109">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="3f299-110">Önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="3f299-110">In the previous code:</span></span>

* <span data-ttu-id="3f299-111">`[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` .</span><span class="sxs-lookup"><span data-stu-id="3f299-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="3f299-112">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="3f299-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="3f299-113">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) özniteliği bir alanın görünen adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="3f299-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="3f299-114">Yukarıdaki kodda, "ReleaseDate" yerine "Yayın tarihi".</span><span class="sxs-lookup"><span data-stu-id="3f299-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="3f299-115">[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği, veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3f299-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3f299-116">Alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="3f299-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="3f299-117">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3f299-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="3f299-118">Hedef URL 'yi görmek için *sayfalara/filmlere* gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="3f299-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si https://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="3f299-120">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantıları, *Sayfalar/filmler/ Index . cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3f299-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="3f299-121">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="3f299-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="3f299-122">Yukarıdaki kodda, [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , sayfada HTML öznitelik değerini dinamik olarak oluşturur `href` Razor (yol göreli olur),, `asp-page` ve yol tanımlayıcısı ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="3f299-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="3f299-123">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="3f299-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="3f299-124">Oluşturulan biçimlendirmeyi incelemek için bir tarayıcıdan **Görünüm kaynağı** ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="3f299-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="3f299-125">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3f299-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="3f299-126">Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle iletir.</span><span class="sxs-lookup"><span data-stu-id="3f299-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="3f299-127">Örneğin, `?id=1` içinde `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="3f299-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="3f299-128">Rota şablonu Ekle</span><span class="sxs-lookup"><span data-stu-id="3f299-128">Add route template</span></span>

<span data-ttu-id="3f299-129">RazorYol şablonunu kullanmak Için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin `{id:int}` .</span><span class="sxs-lookup"><span data-stu-id="3f299-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="3f299-130">Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="3f299-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="3f299-131">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3f299-131">Run the app and then view source.</span></span>

<span data-ttu-id="3f299-132">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="3f299-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="3f299-133">Tamsayı içermeyen yol şablonuna sahip sayfaya yönelik bir istek, `{id:int}` HTTP **not** 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3f299-134">Örneğin, `https://localhost:5001/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="3f299-135">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3f299-136">Davranışını test edin `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="3f299-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="3f299-137">*Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="3f299-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="3f299-138">`public async Task<IActionResult> OnGetAsync(int? id)` *Sayfalarda/filmlerde/details. cshtml. cs*' de bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="3f299-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="3f299-139">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3f299-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="3f299-140">`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="3f299-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="3f299-141">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="3f299-142">Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="3f299-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="3f299-143">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="3f299-143">Review concurrency exception handling</span></span>

<span data-ttu-id="3f299-144">`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="3f299-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="3f299-145">Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="3f299-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="3f299-146">Bloğu test etmek için `catch` :</span><span class="sxs-lookup"><span data-stu-id="3f299-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="3f299-147">Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)` .</span><span class="sxs-lookup"><span data-stu-id="3f299-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="3f299-148">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="3f299-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="3f299-149">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="3f299-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="3f299-150">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="3f299-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="3f299-151">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="3f299-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="3f299-152">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="3f299-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="3f299-153">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="3f299-153">Posting and binding review</span></span>

<span data-ttu-id="3f299-154">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="3f299-155">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında, örneğin `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="3f299-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="3f299-156">`OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="3f299-157">`Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="3f299-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="3f299-158">*Pages/filmler/Edit. cshtml* dosyası model yönergesini içerir `@model RazorPagesMovie.Pages.Movies.EditModel` , bu da film modelini sayfada kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3f299-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="3f299-159">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3f299-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="3f299-160">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="3f299-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="3f299-161">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3f299-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="3f299-162">`[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="3f299-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="3f299-163">Model durumunda hatalar varsa, örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa, form gönderilen değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3f299-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="3f299-164">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f299-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="3f299-165">IndexSayfalarında, oluşturma ve silme gıbı http get yöntemleri Razor benzer bir düzene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3f299-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="3f299-166">`OnPostAsync`Oluşturma SAYFASıNDAKI http post yöntemi, Razor `OnPostAsync` düzenleme sayfasındaki yöntemine benzer bir düzen izler Razor .</span><span class="sxs-lookup"><span data-stu-id="3f299-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f299-167">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3f299-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3f299-168">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="3f299-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f299-169">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="3f299-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="3f299-170">**ReleaseDate** iki sözcük, **Yayın tarihi** olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3f299-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Chrome 'da açık film uygulaması](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="3f299-172">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="3f299-172">Update the generated code</span></span>

<span data-ttu-id="3f299-173">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="3f299-174">`[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` .</span><span class="sxs-lookup"><span data-stu-id="3f299-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="3f299-175">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="3f299-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="3f299-176">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3f299-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="3f299-177">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) özniteliği, "ReleaseDate" yerine "Yayın tarihi" Bu örnekte, bir alanın adı için nelerin gösterileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3f299-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="3f299-178">[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ), bu nedenle alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="3f299-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="3f299-179">Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="3f299-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="3f299-181">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantıları, *Sayfalar/filmler/ Index . cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3f299-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="3f299-182">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="3f299-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="3f299-183">Yukarıdaki kodda, `AnchorTagHelper` HTML öznitelik değerini sayfadan dinamik olarak oluşturur `href` Razor (yol göreli olur),, `asp-page` ve yol kimliği ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="3f299-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="3f299-184">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="3f299-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="3f299-185">Oluşturulan biçimlendirmeyi incelemek için bir tarayıcıdan **Görünüm kaynağı** ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="3f299-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="3f299-186">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3f299-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="3f299-187">Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle iletir.</span><span class="sxs-lookup"><span data-stu-id="3f299-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="3f299-188">Örneğin, `?id=1` içinde  `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="3f299-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="3f299-189">Razor"{İd: int}" yol şablonunu kullanmak Için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3f299-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="3f299-190">Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="3f299-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="3f299-191">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3f299-191">Run the app and then view source.</span></span> <span data-ttu-id="3f299-192">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="3f299-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="3f299-193">Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="3f299-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3f299-194">Örneğin, `https://localhost:5001/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="3f299-195">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3f299-196">Davranışını test etmek için `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="3f299-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="3f299-197">*Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="3f299-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="3f299-198">`public async Task<IActionResult> OnGetAsync(int? id)` *Sayfalarda/filmlerde/details. cshtml. cs*' de bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="3f299-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="3f299-199">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3f299-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="3f299-200">`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="3f299-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="3f299-201">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="3f299-202">Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="3f299-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="3f299-203">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="3f299-203">Review concurrency exception handling</span></span>

<span data-ttu-id="3f299-204">`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="3f299-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="3f299-205">Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="3f299-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="3f299-206">Bloğu test etmek için `catch` :</span><span class="sxs-lookup"><span data-stu-id="3f299-206">To test the `catch` block:</span></span>

* <span data-ttu-id="3f299-207">Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="3f299-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="3f299-208">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="3f299-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="3f299-209">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="3f299-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="3f299-210">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="3f299-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="3f299-211">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="3f299-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="3f299-212">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="3f299-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="3f299-213">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="3f299-213">Posting and binding review</span></span>

<span data-ttu-id="3f299-214">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3f299-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="3f299-215">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında, örneğin `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="3f299-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="3f299-216">`OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f299-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="3f299-217">`Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="3f299-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="3f299-218">*Pages/filmler/Edit. cshtml* dosyası model yönergesini içerir `@model RazorPagesMovie.Pages.Movies.EditModel` , bu da film modelini sayfada kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3f299-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="3f299-219">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3f299-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="3f299-220">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="3f299-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="3f299-221">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3f299-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="3f299-222">`[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="3f299-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="3f299-223">Model durumunda hatalar varsa, örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa, form gönderilen değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3f299-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="3f299-224">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f299-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="3f299-225">IndexSayfalarında, oluşturma ve silme gıbı http get yöntemleri Razor benzer bir düzene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3f299-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="3f299-226">`OnPostAsync`Oluşturma SAYFASıNDAKI http post yöntemi, Razor `OnPostAsync` düzenleme sayfasındaki yöntemine benzer bir düzen izler Razor .</span><span class="sxs-lookup"><span data-stu-id="3f299-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="3f299-227">Arama sonraki öğreticiye eklenir.</span><span class="sxs-lookup"><span data-stu-id="3f299-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f299-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3f299-228">Additional resources</span></span>

* [<span data-ttu-id="3f299-229">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="3f299-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="3f299-230">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="3f299-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
