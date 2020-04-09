---
title: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında oluşturulan sayfaları nasıl güncelleştirtini öğrenin.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666217"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="8fdec-103">ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8fdec-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="8fdec-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8fdec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8fdec-105">İskele film uygulaması iyi bir başlangıç vardır, ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="8fdec-106">**Yayın Tarihi** **Çıkış Tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome'da film uygulaması açıldı](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="8fdec-108">Oluşturulan kodu güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8fdec-108">Update the generated code</span></span>

<span data-ttu-id="8fdec-109">*Modeller/Movie.cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanan satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="8fdec-110">Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklama, Entity Framework Core'un `Price` veritabanındaki para birimine doğru şekilde eşlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="8fdec-111">Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)</span><span class="sxs-lookup"><span data-stu-id="8fdec-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="8fdec-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğretici de ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="8fdec-113">[Ekran](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği, bir alanın adı için ne görüntüleneceklerini belirtir (bu durumda "Yayın Tarihi" yerine "Yayın Tarihi" ).</span><span class="sxs-lookup"><span data-stu-id="8fdec-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="8fdec-114">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü (Tarih) belirtir, böylece alanda depolanan saat bilgileri görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="8fdec-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="8fdec-115">Sayfalara/Filmlere göz atın ve hedef URL'yi görmek için bir **Edit** bağlantısının üzerine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Edit bağlantısı üzerinde fare ile tarayıcı penceresi http://localhost:1234/Movies/Edit/5 ve bir bağlantı Url gösterilir](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="8fdec-117">**Düzenleme,** **Ayrıntılar**ve **Sil** *bağlantıları, Sayfalar/Filmler/Index.cshtml* dosyasındaki [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8fdec-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="8fdec-118">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="8fdec-119">`AnchorTagHelper` Önceki kodda, dinamik olarak Razor Page `href` (rota göreceli), `asp-page`ve rota id ( HTML`asp-route-id`öznitelik değeri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8fdec-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="8fdec-120">Daha fazla bilgi [için Sayfalar için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) adresine bakın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="8fdec-121">Oluşturulan biçimlendirmeyi incelemek için favori tarayıcınızdan **Kaynak Görünümü'nu** kullanın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="8fdec-122">Oluşturulan HTML'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="8fdec-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="8fdec-123">Dinamik olarak oluşturulan bağlantılar film kimliğini sorgu dizesiyle (örneğin, `?id=1` giriş) `https://localhost:5001/Movies/Details?id=1`geçirir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="8fdec-124">Rota şablonu ekleme</span><span class="sxs-lookup"><span data-stu-id="8fdec-124">Add route template</span></span>

<span data-ttu-id="8fdec-125">"{id:int}" rota şablonunu kullanmak için Düzenle, Ayrıntıları ve Jilet Sayfalarını Sil'i güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="8fdec-126">Bu sayfaların her biri için `@page` `@page "{id:int}"`sayfa yönergesini ' den ' e değiştirin</span><span class="sxs-lookup"><span data-stu-id="8fdec-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="8fdec-127">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-127">Run the app and then view source.</span></span> <span data-ttu-id="8fdec-128">Oluşturulan HTML, kimliği URL'nin yol bölümüne ekler:</span><span class="sxs-lookup"><span data-stu-id="8fdec-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="8fdec-129">Tamsayı içermeyen "{id:int}" rota şablonuna **not** sahip sayfaya gelen bir istek, http 404 (bulunamadı) hatasını döndürecektir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="8fdec-130">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürecek.</span><span class="sxs-lookup"><span data-stu-id="8fdec-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="8fdec-131">Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:</span><span class="sxs-lookup"><span data-stu-id="8fdec-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="8fdec-132">Davranışını test `@page "{id:int?}"`etmek için:</span><span class="sxs-lookup"><span data-stu-id="8fdec-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="8fdec-133">*Sayfalar/Filmler/Details.cshtml'deki* sayfa yönergesini `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="8fdec-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="8fdec-134">Bir kesme noktası `public async Task<IActionResult> OnGetAsync(int? id)` ayarlayın *(Sayfalar/Filmler/Ayrıntılar.cshtml.cs*içinde).</span><span class="sxs-lookup"><span data-stu-id="8fdec-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="8fdec-135">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="8fdec-136">Direktifle, `@page "{id:int}"` kırılma noktası asla vurulmaz.</span><span class="sxs-lookup"><span data-stu-id="8fdec-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="8fdec-137">Yönlendirme motoru HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="8fdec-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="8fdec-138">Kullanarak, `@page "{id:int?}"` `OnGetAsync` yöntem `NotFound` döndürür (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="8fdec-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="8fdec-139">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="8fdec-139">Review concurrency exception handling</span></span>

<span data-ttu-id="8fdec-140">`OnPostAsync` *Sayfalar/Filmler/Edit.cshtml.cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="8fdec-141">Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmdeki değişiklikleri yayınladığında eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="8fdec-142">`catch` Bloğu test etmek için:</span><span class="sxs-lookup"><span data-stu-id="8fdec-142">To test the `catch` block:</span></span>

* <span data-ttu-id="8fdec-143">Kesme noktasını ayarlama`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="8fdec-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="8fdec-144">Film için **Edit'i** seçin, değişiklik yapın, ancak **Kaydet'i**girmeyin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="8fdec-145">Başka bir tarayıcı penceresinde, aynı film için **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="8fdec-146">Önceki tarayıcı penceresinde, filmdeki değişiklikleri yayınlayın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="8fdec-147">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="8fdec-148">Bkz. Daha fazla bilgi için [eşzamanlılık çakışmalarını ele alın.](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="8fdec-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="8fdec-149">Gönderme ve bağlayıcı inceleme</span><span class="sxs-lookup"><span data-stu-id="8fdec-149">Posting and binding review</span></span>

<span data-ttu-id="8fdec-150">*Sayfalar/Filmler/Edit.cshtml.cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="8fdec-151">Filmler/Edit sayfasına http GET isteği yapıldığında (örneğin,): `http://localhost:5000/Movies/Edit/2`</span><span class="sxs-lookup"><span data-stu-id="8fdec-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="8fdec-152">Yöntem, `OnGetAsync` filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="8fdec-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="8fdec-153">Yöntem, `Page` *Sayfaları/Filmleri/Edit.cshtml* Jilet Sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="8fdec-154">*Sayfalar/Filmler/Edit.cshtml* dosyası, film modelini sayfada kullanılabilir kılan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`), içerir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="8fdec-155">Edit formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="8fdec-156">Filmler/Edit sayfası yayınlandığında:</span><span class="sxs-lookup"><span data-stu-id="8fdec-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="8fdec-157">Sayfadaki form değerleri `Movie` özelliğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="8fdec-158">Öznitelik `[BindProperty]` Model [bağlama](xref:mvc/models/model-binding)sağlar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="8fdec-159">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="8fdec-160">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="8fdec-161">Index, Create ve Delete Razor sayfalarındaki HTTP GET yöntemleri de benzer bir örüntü izler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="8fdec-162">Jilet `OnPostAsync` Oluştur Sayfasındaki HTTP POST yöntemi, `OnPostAsync` Razor Sayfasını Düzenle'deki yönteme benzer bir örüntü izler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fdec-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8fdec-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8fdec-164">[Önceki: Veritabanıyla](xref:tutorials/razor-pages/sql)
> Çalışma[Sonraki: Arama ekleme](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="8fdec-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8fdec-165">İskele film uygulaması iyi bir başlangıç vardır, ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="8fdec-166">**Yayın Tarihi** **Çıkış Tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome'da film uygulaması açıldı](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="8fdec-168">Oluşturulan kodu güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8fdec-168">Update the generated code</span></span>

<span data-ttu-id="8fdec-169">*Modeller/Movie.cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanan satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="8fdec-170">Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklama, Entity Framework Core'un `Price` veritabanındaki para birimine doğru şekilde eşlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="8fdec-171">Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)</span><span class="sxs-lookup"><span data-stu-id="8fdec-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="8fdec-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğretici de ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="8fdec-173">[Ekran](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği, bir alanın adı için ne görüntüleneceklerini belirtir (bu durumda "Yayın Tarihi" yerine "Yayın Tarihi" ).</span><span class="sxs-lookup"><span data-stu-id="8fdec-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="8fdec-174">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü (Tarih) belirtir, böylece alanda depolanan saat bilgileri görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="8fdec-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="8fdec-175">Sayfalara/Filmlere göz atın ve hedef URL'yi görmek için bir **Edit** bağlantısının üzerine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Edit bağlantısı üzerinde fare ile tarayıcı penceresi http://localhost:1234/Movies/Edit/5 ve bir bağlantı Url gösterilir](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="8fdec-177">**Düzenleme,** **Ayrıntılar**ve **Sil** *bağlantıları, Sayfalar/Filmler/Index.cshtml* dosyasındaki [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8fdec-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="8fdec-178">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="8fdec-179">`AnchorTagHelper` Önceki kodda, dinamik olarak Razor Page `href` (rota göreceli), `asp-page`ve rota id ( HTML`asp-route-id`öznitelik değeri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8fdec-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="8fdec-180">Daha fazla bilgi [için Sayfalar için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) adresine bakın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="8fdec-181">Oluşturulan biçimlendirmeyi incelemek için favori tarayıcınızdan **Kaynak Görünümü'nu** kullanın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="8fdec-182">Oluşturulan HTML'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="8fdec-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="8fdec-183">Dinamik olarak oluşturulan bağlantılar film kimliğini sorgu dizesiyle (örneğin, `?id=1` giriş) `https://localhost:5001/Movies/Details?id=1`geçirir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="8fdec-184">"{id:int}" rota şablonunu kullanmak için Düzenle, Ayrıntıları ve Jilet Sayfalarını Sil'i güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="8fdec-185">Bu sayfaların her biri için `@page` `@page "{id:int}"`sayfa yönergesini ' den ' e değiştirin</span><span class="sxs-lookup"><span data-stu-id="8fdec-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="8fdec-186">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-186">Run the app and then view source.</span></span> <span data-ttu-id="8fdec-187">Oluşturulan HTML, kimliği URL'nin yol bölümüne ekler:</span><span class="sxs-lookup"><span data-stu-id="8fdec-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="8fdec-188">Tamsayı içermeyen "{id:int}" rota şablonuna **not** sahip sayfaya gelen bir istek, http 404 (bulunamadı) hatasını döndürecektir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="8fdec-189">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürecek.</span><span class="sxs-lookup"><span data-stu-id="8fdec-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="8fdec-190">Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:</span><span class="sxs-lookup"><span data-stu-id="8fdec-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="8fdec-191">Davranışını test `@page "{id:int?}"`etmek için:</span><span class="sxs-lookup"><span data-stu-id="8fdec-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="8fdec-192">*Sayfalar/Filmler/Details.cshtml'deki* sayfa yönergesini `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="8fdec-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="8fdec-193">Bir kesme noktası `public async Task<IActionResult> OnGetAsync(int? id)` ayarlayın *(Sayfalar/Filmler/Ayrıntılar.cshtml.cs*içinde).</span><span class="sxs-lookup"><span data-stu-id="8fdec-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="8fdec-194">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="8fdec-195">Direktifle, `@page "{id:int}"` kırılma noktası asla vurulmaz.</span><span class="sxs-lookup"><span data-stu-id="8fdec-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="8fdec-196">Yönlendirme motoru HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="8fdec-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="8fdec-197">Kullanarak, `@page "{id:int?}"` `OnGetAsync` yöntem `NotFound` döndürür (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="8fdec-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="8fdec-198">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="8fdec-198">Review concurrency exception handling</span></span>

<span data-ttu-id="8fdec-199">`OnPostAsync` *Sayfalar/Filmler/Edit.cshtml.cs* dosyasındaki yöntemi gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="8fdec-200">Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmdeki değişiklikleri yayınladığında eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="8fdec-201">`catch` Bloğu test etmek için:</span><span class="sxs-lookup"><span data-stu-id="8fdec-201">To test the `catch` block:</span></span>

* <span data-ttu-id="8fdec-202">Kesme noktasını ayarlama`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="8fdec-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="8fdec-203">Film için **Edit'i** seçin, değişiklik yapın, ancak **Kaydet'i**girmeyin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="8fdec-204">Başka bir tarayıcı penceresinde, aynı film için **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="8fdec-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="8fdec-205">Önceki tarayıcı penceresinde, filmdeki değişiklikleri yayınlayın.</span><span class="sxs-lookup"><span data-stu-id="8fdec-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="8fdec-206">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="8fdec-207">Bkz. Daha fazla bilgi için [eşzamanlılık çakışmalarını ele alın.](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="8fdec-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="8fdec-208">Gönderme ve bağlayıcı inceleme</span><span class="sxs-lookup"><span data-stu-id="8fdec-208">Posting and binding review</span></span>

<span data-ttu-id="8fdec-209">*Sayfalar/Filmler/Edit.cshtml.cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8fdec-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="8fdec-210">Filmler/Edit sayfasına http GET isteği yapıldığında (örneğin,): `http://localhost:5000/Movies/Edit/2`</span><span class="sxs-lookup"><span data-stu-id="8fdec-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="8fdec-211">Yöntem, `OnGetAsync` filmi veritabanından getirir ve `Page` yöntemi döndürür.</span><span class="sxs-lookup"><span data-stu-id="8fdec-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="8fdec-212">Yöntem, `Page` *Sayfaları/Filmleri/Edit.cshtml* Jilet Sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="8fdec-213">*Sayfalar/Filmler/Edit.cshtml* dosyası, film modelini sayfada kullanılabilir kılan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`), içerir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="8fdec-214">Edit formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="8fdec-215">Filmler/Edit sayfası yayınlandığında:</span><span class="sxs-lookup"><span data-stu-id="8fdec-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="8fdec-216">Sayfadaki form değerleri `Movie` özelliğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="8fdec-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="8fdec-217">Öznitelik `[BindProperty]` Model [bağlama](xref:mvc/models/model-binding)sağlar.</span><span class="sxs-lookup"><span data-stu-id="8fdec-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="8fdec-218">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="8fdec-219">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="8fdec-220">Index, Create ve Delete Razor sayfalarındaki HTTP GET yöntemleri de benzer bir örüntü izler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="8fdec-221">Jilet `OnPostAsync` Oluştur Sayfasındaki HTTP POST yöntemi, `OnPostAsync` Razor Sayfasını Düzenle'deki yönteme benzer bir örüntü izler.</span><span class="sxs-lookup"><span data-stu-id="8fdec-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="8fdec-222">Arama sonraki öğreticieklenir.</span><span class="sxs-lookup"><span data-stu-id="8fdec-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fdec-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8fdec-223">Additional resources</span></span>

* [<span data-ttu-id="8fdec-224">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="8fdec-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="8fdec-225">[Önceki: Veritabanıyla](xref:tutorials/razor-pages/sql)
> Çalışma[Sonraki: Arama ekleme](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="8fdec-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
