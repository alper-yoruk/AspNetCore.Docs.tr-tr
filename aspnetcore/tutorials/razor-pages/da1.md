---
title: 5. bölüm, ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 5. bölümü :::no-loc(Razor)::: .'
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7d25dae67c928fa659654ce4ab34cfdad08b5300
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060072"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="acb04-103">5. bölüm, ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="acb04-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="acb04-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="acb04-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="acb04-105">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="acb04-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="acb04-106">**ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="acb04-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="acb04-108">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="acb04-108">Update the generated code</span></span>

<span data-ttu-id="acb04-109">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="acb04-110">`[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` .</span><span class="sxs-lookup"><span data-stu-id="acb04-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="acb04-111">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="acb04-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="acb04-112">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="acb04-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="acb04-113">[Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="acb04-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="acb04-114">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="acb04-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="acb04-115">Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="acb04-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="acb04-117">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="acb04-117">The **Edit** , **Details** , and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="acb04-118">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="acb04-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="acb04-119">Yukarıdaki kodda, `AnchorTagHelper` HTML öznitelik değerini sayfadan dinamik olarak oluşturur `href` :::no-loc(Razor)::: (yol göreli olur),, `asp-page` ve yol kimliği ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="acb04-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the :::no-loc(Razor)::: Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="acb04-120">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="acb04-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="acb04-121">Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın.</span><span class="sxs-lookup"><span data-stu-id="acb04-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="acb04-122">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="acb04-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="acb04-123">Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle (örneğin, `?id=1` içinde  `https://localhost:5001/Movies/Details?id=1` ) iletir.</span><span class="sxs-lookup"><span data-stu-id="acb04-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="acb04-124">Rota şablonu Ekle</span><span class="sxs-lookup"><span data-stu-id="acb04-124">Add route template</span></span>

<span data-ttu-id="acb04-125">:::no-loc(Razor):::"{İd: int}" yol şablonunu kullanmak Için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="acb04-125">Update the Edit, Details, and Delete :::no-loc(Razor)::: Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="acb04-126">Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="acb04-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="acb04-127">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="acb04-127">Run the app and then view source.</span></span> <span data-ttu-id="acb04-128">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="acb04-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="acb04-129">Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="acb04-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="acb04-130">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="acb04-131">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="acb04-132">Davranışını test etmek için `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="acb04-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="acb04-133">*Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="acb04-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="acb04-134">İçinde bir kesme noktası ayarlayın `public async Task<IActionResult> OnGetAsync(int? id)` ( *sayfalarda/filmlerde/details. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="acb04-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs* ).</span></span>
* <span data-ttu-id="acb04-135">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="acb04-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="acb04-136">`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="acb04-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="acb04-137">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="acb04-138">Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="acb04-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="acb04-139">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="acb04-139">Review concurrency exception handling</span></span>

<span data-ttu-id="acb04-140">`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="acb04-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="acb04-141">Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="acb04-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="acb04-142">Bloğu test etmek için `catch` :</span><span class="sxs-lookup"><span data-stu-id="acb04-142">To test the `catch` block:</span></span>

* <span data-ttu-id="acb04-143">Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="acb04-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="acb04-144">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet** ' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="acb04-144">Select **Edit** for a movie, make changes, but don't enter **Save** .</span></span>
* <span data-ttu-id="acb04-145">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="acb04-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="acb04-146">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="acb04-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="acb04-147">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="acb04-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="acb04-148">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="acb04-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="acb04-149">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="acb04-149">Posting and binding review</span></span>

<span data-ttu-id="acb04-150">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="acb04-151">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="acb04-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="acb04-152">`OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="acb04-153">`Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* :::no-loc(Razor)::: sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="acb04-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="acb04-154">*Pages/filmler/Edit. cshtml* dosyası, `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel` film modelinin sayfada kullanılabilir olmasını sağlayan model yönergesini () içerir.</span><span class="sxs-lookup"><span data-stu-id="acb04-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="acb04-155">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="acb04-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="acb04-156">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="acb04-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="acb04-157">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="acb04-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="acb04-158">`[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="acb04-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="acb04-159">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="acb04-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="acb04-160">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="acb04-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="acb04-161">Dizin, oluşturma ve silme sayfalarında HTTP GET yöntemleri :::no-loc(Razor)::: benzer bir düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="acb04-161">The HTTP GET methods in the Index, Create, and Delete :::no-loc(Razor)::: pages follow a similar pattern.</span></span> <span data-ttu-id="acb04-162">`OnPostAsync`Oluşturma SAYFASıNDAKI http post yöntemi, :::no-loc(Razor)::: `OnPostAsync` düzenleme sayfasındaki yöntemine benzer bir düzen izler :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="acb04-162">The HTTP POST `OnPostAsync` method in the Create :::no-loc(Razor)::: Page follows a similar pattern to the `OnPostAsync` method in the Edit :::no-loc(Razor)::: Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acb04-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="acb04-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="acb04-164">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="acb04-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="acb04-165">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="acb04-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="acb04-166">**ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="acb04-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="acb04-168">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="acb04-168">Update the generated code</span></span>

<span data-ttu-id="acb04-169">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="acb04-170">`[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` .</span><span class="sxs-lookup"><span data-stu-id="acb04-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="acb04-171">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="acb04-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="acb04-172">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="acb04-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="acb04-173">[Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="acb04-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="acb04-174">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="acb04-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="acb04-175">Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="acb04-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="acb04-177">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="acb04-177">The **Edit** , **Details** , and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="acb04-178">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="acb04-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="acb04-179">Yukarıdaki kodda, `AnchorTagHelper` HTML öznitelik değerini sayfadan dinamik olarak oluşturur `href` :::no-loc(Razor)::: (yol göreli olur),, `asp-page` ve yol kimliği ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="acb04-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the :::no-loc(Razor)::: Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="acb04-180">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="acb04-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="acb04-181">Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın.</span><span class="sxs-lookup"><span data-stu-id="acb04-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="acb04-182">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="acb04-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="acb04-183">Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle (örneğin, `?id=1` içinde  `https://localhost:5001/Movies/Details?id=1` ) iletir.</span><span class="sxs-lookup"><span data-stu-id="acb04-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="acb04-184">:::no-loc(Razor):::"{İd: int}" yol şablonunu kullanmak Için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="acb04-184">Update the Edit, Details, and Delete :::no-loc(Razor)::: Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="acb04-185">Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="acb04-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="acb04-186">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="acb04-186">Run the app and then view source.</span></span> <span data-ttu-id="acb04-187">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="acb04-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="acb04-188">Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="acb04-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="acb04-189">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="acb04-190">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="acb04-191">Davranışını test etmek için `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="acb04-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="acb04-192">*Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="acb04-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="acb04-193">İçinde bir kesme noktası ayarlayın `public async Task<IActionResult> OnGetAsync(int? id)` ( *sayfalarda/filmlerde/details. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="acb04-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs* ).</span></span>
* <span data-ttu-id="acb04-194">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="acb04-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="acb04-195">`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="acb04-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="acb04-196">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="acb04-197">Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="acb04-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="acb04-198">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="acb04-198">Review concurrency exception handling</span></span>

<span data-ttu-id="acb04-199">`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="acb04-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="acb04-200">Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="acb04-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="acb04-201">Bloğu test etmek için `catch` :</span><span class="sxs-lookup"><span data-stu-id="acb04-201">To test the `catch` block:</span></span>

* <span data-ttu-id="acb04-202">Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="acb04-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="acb04-203">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet** ' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="acb04-203">Select **Edit** for a movie, make changes, but don't enter **Save** .</span></span>
* <span data-ttu-id="acb04-204">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="acb04-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="acb04-205">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="acb04-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="acb04-206">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="acb04-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="acb04-207">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="acb04-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="acb04-208">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="acb04-208">Posting and binding review</span></span>

<span data-ttu-id="acb04-209">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="acb04-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="acb04-210">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="acb04-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="acb04-211">`OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="acb04-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="acb04-212">`Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* :::no-loc(Razor)::: sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="acb04-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="acb04-213">*Pages/filmler/Edit. cshtml* dosyası, `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel` film modelinin sayfada kullanılabilir olmasını sağlayan model yönergesini () içerir.</span><span class="sxs-lookup"><span data-stu-id="acb04-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="acb04-214">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="acb04-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="acb04-215">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="acb04-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="acb04-216">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="acb04-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="acb04-217">`[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="acb04-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="acb04-218">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="acb04-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="acb04-219">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="acb04-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="acb04-220">Dizin, oluşturma ve silme sayfalarında HTTP GET yöntemleri :::no-loc(Razor)::: benzer bir düzene uyar.</span><span class="sxs-lookup"><span data-stu-id="acb04-220">The HTTP GET methods in the Index, Create, and Delete :::no-loc(Razor)::: pages follow a similar pattern.</span></span> <span data-ttu-id="acb04-221">`OnPostAsync`Oluşturma SAYFASıNDAKI http post yöntemi, :::no-loc(Razor)::: `OnPostAsync` düzenleme sayfasındaki yöntemine benzer bir düzen izler :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="acb04-221">The HTTP POST `OnPostAsync` method in the Create :::no-loc(Razor)::: Page follows a similar pattern to the `OnPostAsync` method in the Edit :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="acb04-222">Arama sonraki öğreticiye eklenir.</span><span class="sxs-lookup"><span data-stu-id="acb04-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acb04-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="acb04-223">Additional resources</span></span>

* [<span data-ttu-id="acb04-224">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="acb04-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="acb04-225">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="acb04-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
