---
title: ASP.NET Core MVC uygulamasına görünüm ekleme
author: rick-anderson
description: Basit bir ASP.NET Core MVC uygulamasına görünüm ekleme
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660211"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b2b95-103">ASP.NET Core MVC uygulamasına görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="b2b95-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b2b95-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2b95-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2b95-105">Bu bölümde, istemciye HTML yanıtları oluşturma işlemini temiz bir şekilde kapsüllemek için sınıfı `HelloWorldController` [Razor](xref:mvc/views/razor) görünüm dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b2b95-106">Razor kullanarak bir görünüm şablon dosyası oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-106">You create a view template file using Razor.</span></span> <span data-ttu-id="b2b95-107">Jilet tabanlı görünüm şablonlarında *.cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b2b95-108">C# ile HTML çıktısı oluşturmak için zarif bir yol sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="b2b95-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b2b95-109">Şu `Index` anda yöntem denetleyici sınıfında sabit kodlanmış bir ileti ile bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="b2b95-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b2b95-110">`HelloWorldController` Sınıfta, `Index` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b2b95-111">Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b2b95-112">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b2b95-113">Denetleyici yöntemleri *(eylem yöntemleri*olarak da `Index` bilinir), yukarıdaki yöntem <xref:Microsoft.AspNetCore.Mvc.IActionResult> gibi, genellikle <xref:Microsoft.AspNetCore.Mvc.ActionResult>bir (veya `string`türetilen bir sınıf), gibi bir tür değil döndürür .</span><span class="sxs-lookup"><span data-stu-id="b2b95-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b2b95-114">Görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="b2b95-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2b95-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2b95-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2b95-116">*Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.</span><span class="sxs-lookup"><span data-stu-id="b2b95-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b2b95-117">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **yeni > öğe ekleyin.**</span><span class="sxs-lookup"><span data-stu-id="b2b95-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b2b95-118">Yeni **Öğe Ekle - MvcMovie** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="b2b95-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b2b95-119">Sağ üstteki arama kutusuna, *görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="b2b95-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b2b95-120">**Jilet Görünümü'nü** seçin</span><span class="sxs-lookup"><span data-stu-id="b2b95-120">Select **Razor View**</span></span>

  * <span data-ttu-id="b2b95-121">**Ad** kutusu değerini tutun, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b2b95-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b2b95-122">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="b2b95-122">Select **Add**</span></span>

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2b95-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2b95-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2b95-125">Için `Index` bir görünüm `HelloWorldController`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b2b95-126">*Görünümler/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b2b95-127">*Görünümler/HelloWorld* klasör adı *Index.cshtml'e*yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2b95-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2b95-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2b95-129">*Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.</span><span class="sxs-lookup"><span data-stu-id="b2b95-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b2b95-130">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **Yeni > Dosya Ekle'** ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b2b95-131">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="b2b95-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b2b95-132">Sol bölmede **ASP .NET Core'u** seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="b2b95-133">Orta bölmede **MVC Görünüm Sayfası'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="b2b95-134">**Ad** kutusuna *Dizin* yazın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="b2b95-135">**Yeni'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-135">Select **New**.</span></span>

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="b2b95-137">*Görünümler/HelloWorld/Index.cshtml* Razor görünüm dosyasının içeriğini aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b2b95-138">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b2b95-139">Yöntem `Index` çok `HelloWorldController` yapmadı; bu yöntem `return View();`tarayıcıya bir yanıt işlemek için bir görünüm şablondosyası kullanması gerektiğini belirten deyimi koştu.</span><span class="sxs-lookup"><span data-stu-id="b2b95-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b2b95-140">Görünüm şablonu dosya adı belirtilmedi, MVC varsayılan görünüm dosyasını kullanmaya varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="b2b95-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b2b95-141">Varsayılan görünüm dosyası yöntemle aynı ada sahiptir (`Index`), bu nedenle */Views/HelloWorld/Index.cshtml* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b2b95-142">Aşağıdaki resimde "Görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b2b95-143">görünümünde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="b2b95-143">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b2b95-145">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b2b95-145">Change views and layout pages</span></span>

<span data-ttu-id="b2b95-146">Menü bağlantılarını seçin **(MvcMovie**, **Home**, ve **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="b2b95-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b2b95-147">Her sayfa aynı menü düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="b2b95-148">Menü düzeni *Görünümler/Paylaşılan/_Layout.cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b2b95-149">*Görünümler/Paylaşılan/_Layout.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b2b95-150">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b2b95-151">`@RenderBody()` Hattı bul.</span><span class="sxs-lookup"><span data-stu-id="b2b95-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b2b95-152">`RenderBody`oluşturduğunuz tüm görünüme özel sayfaların düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b2b95-153">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Ev/Gizlilik.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b2b95-154">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b2b95-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="b2b95-155">*Görünümler/Paylaşılan/_Layout.cshtml* dosyasının içeriğini aşağıdaki biçimlendirmeyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="b2b95-156">Değişiklikler vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="b2b95-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="b2b95-157">Önceki biçimlendirme aşağıdaki değişiklikleri yaptı:</span><span class="sxs-lookup"><span data-stu-id="b2b95-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="b2b95-158">3 oluşumları `MvcMovie` `Movie App`için .</span><span class="sxs-lookup"><span data-stu-id="b2b95-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b2b95-159">Bağlantı `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`elemanı.</span><span class="sxs-lookup"><span data-stu-id="b2b95-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b2b95-160">Önceki biçimlendirmede, `asp-area=""` bu uygulama [Alanlar'ı](xref:mvc/controllers/areas)kullanmadığından, bağlantı noktası [Tag Helper özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ve öznitelik değeri atlandı.</span><span class="sxs-lookup"><span data-stu-id="b2b95-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="b2b95-161">**Not**: `Movies` Denetleyici uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="b2b95-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b2b95-162">Bu noktada, `Movie App` bağlantı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b2b95-163">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b2b95-164">Tarayıcı sekmesindeki başlığın **Gizlilik Politikası 'nı** nasıl görüntülenebilene dikkat edin - Gizlilik Politikası yerine Film Uygulaması **- Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="b2b95-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="b2b95-166">Ana **Sayfa** bağlantısını seçin ve başlık ve bağlantı metninin de **Film Uygulamasını**görüntülediğini fark edin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b2b95-167">Biz düzen şablonu bir kez değişiklik yapmak ve sitedeki tüm sayfaları yeni bağlantı metni ve yeni başlık yansıtacak başardık.</span><span class="sxs-lookup"><span data-stu-id="b2b95-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b2b95-168">*Görünümler/_ViewStart.cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b2b95-169">*Views/_ViewStart.cshtml* dosyası, her görünüme *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını getirir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b2b95-170">Özellik, `Layout` farklı bir düzen görünümü ayarlamak veya hiçbir `null` düzen dosyasının kullanılmaması için ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b2b95-171">`<h2>` *Görünümler/HelloWorld/Index.cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b2b95-172">Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod parçasının ekranı değiştirdiğini görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b2b95-173">`ViewData["Title"] = "Movie List";`yukarıdaki kodda sözlüğün `Title` `ViewData` özelliğini "Film Listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b2b95-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b2b95-174">Özellik, `Title` düzen sayfasındaki `<title>` HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b2b95-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b2b95-175">Değişikliği kaydedin `https://localhost:{PORT}/HelloWorld`ve ''ye gidin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b2b95-176">Tarayıcı başlığının, birincil başlığın ve ikincil başlıkların değiştiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b2b95-177">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülenmiş olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b2b95-178">Sunucudan gelen yanıtı yüklemeye zorlamak için tarayıcınızda Ctrl+F5 tuşuna basın.) Tarayıcı `ViewData["Title"]` *başlığı, Index.cshtml* görünüm şablonuna ve düzen dosyasına eklenen ek "- Film Uygulaması" ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b2b95-179">*Index.cshtml* görünüm şablonundaki içerik *Görünümler/Paylaşılan/_Layout.cshtml* görünüm şablonuyla birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="b2b95-180">Tarayıcıya tek bir HTML yanıtı gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="b2b95-181">Düzen şablonları, bir uygulamadaki tüm sayfalarda geçerli olan değişiklikleri yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="b2b95-182">Daha fazla bilgi için [Düzen'e](xref:mvc/views/layout)bakın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Film Listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b2b95-184">Bizim küçük "veri" (bu durumda "Merhaba Bizim Görünüm Şablonu!"</span><span class="sxs-lookup"><span data-stu-id="b2b95-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b2b95-185">mesaj) olsa da, sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="b2b95-185">message) is hard-coded, though.</span></span> <span data-ttu-id="b2b95-186">MVC uygulaması "V" (görünüm) ve bir "C" (denetleyici) var, ama hiçbir "M" (model) henüz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b2b95-187">Denetleyiciden Görünüme Veri Aktarma</span><span class="sxs-lookup"><span data-stu-id="b2b95-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b2b95-188">Denetleyici eylemleri, gelen bir URL isteğine yanıt olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b2b95-189">Denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b2b95-190">Denetleyici bir veri kaynağından veri alır ve tarayıcıya geri göndermek için ne tür bir yanıt karar verir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b2b95-191">Görünüm şablonları tarayıcıya bir HTML yanıtı oluşturmak ve biçimlendirmek için bir denetleyiciden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b2b95-192">Denetleyiciler, bir görünüm şablonuna yanıt verebilmesi için gereken verileri sağlamakla yükümlüdür.</span><span class="sxs-lookup"><span data-stu-id="b2b95-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b2b95-193">En iyi yöntem: Görünüm şablonları iş mantığı **gerçekleştirmemeli** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b2b95-194">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sağlanan verilerle çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b2b95-195">Bu "endişeleri ayırma" bakımı, kodun temiz, sınanabilir ve korunabilir tutulmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b2b95-196">Şu `Welcome` anda, `HelloWorldController` sınıftaki `name` yöntem `ID` bir ve bir parametre alır ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b2b95-197">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, bunun yerine görünüm şablonu kullanmak için denetleyiciyi değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b2b95-198">Görünüm şablonu dinamik bir yanıt oluşturur, bu da yanıtı oluşturmak için uygun veri bitlerinin denetleyiciden görünüme geçirilmesi gerektiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b2b95-199">Denetleyicinin görünüm şablonuna ihtiyaç duyduğu dinamik verileri (parametreleri) görünüm şablonuna erişebileceği bir `ViewData` sözlükte koyarak bunu yapın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b2b95-200">*HelloWorldController.cs,* sözlük `Welcome` için bir `Message` ve `NumTimes` değer `ViewData` eklemek için yöntemi değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b2b95-201">Sözlük `ViewData` dinamik bir nesnedir, bu da herhangi bir türün kullanılabildiği anlamına gelir; nesnenin içine bir şey koyana `ViewData` kadar tanımlı özellikleri yoktur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b2b95-202">[MVC model bağlama sistemi,](xref:mvc/models/model-binding) adres çubuğundaki `numTimes`sorgu dizesinden adı geçen parametreleri (ve)`name` yönteminizdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="b2b95-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b2b95-203">Tam *HelloWorldController.cs* dosyası aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="b2b95-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b2b95-204">Sözlük `ViewData` nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b2b95-205">*Görünümler/HelloWorld/Welcome.cshtml*adlı hoş geldiniz görünümü şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b2b95-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b2b95-206">*Welcome.cshtml* görünüm şablonunda "Merhaba" `NumTimes`görüntülenen bir döngü oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b2b95-207">*Views/HelloWorld/Welcome.cshtml* içeriğini aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b2b95-208">Değişikliklerinizi kaydedin ve aşağıdaki URL'ye göz atın:</span><span class="sxs-lookup"><span data-stu-id="b2b95-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b2b95-209">Veriler URL'den alınır ve [MVC model bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye aktarılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b2b95-210">Denetleyici verileri bir `ViewData` sözlük tesidikkate alır ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b2b95-211">Görünüm daha sonra verileri tarayıcıya HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="b2b95-211">The view then renders the data as HTML to the browser.</span></span>

![Hoş geldiniz etiketini ve Hello Rick ifadesini gösteren gizlilik görünümü dört kez gösterilir](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b2b95-213">Yukarıdaki örnekte, `ViewData` sözlük denetleyiciden bir görünüme veri aktarmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b2b95-214">Daha sonra öğreticide, bir görünüm modeli denetleyiciden görünüme veri aktarmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b2b95-215">Veri aktarılabilmek için görünüm modeli yaklaşımı `ViewData` genellikle sözlük yaklaşımına göre daha çok tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b2b95-216">Daha fazla bilgi [için ViewBag, ViewData veya TempData'nın ne zaman kullanılacağına](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) bakın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b2b95-217">Bir sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b2b95-218">[Önceki](adding-controller.md)
> [Sonraki](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b2b95-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2b95-219">Bu bölümde, istemciye HTML yanıtları oluşturma işlemini temiz bir şekilde kapsüllemek için sınıfı `HelloWorldController` [Razor](xref:mvc/views/razor) görünüm dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b2b95-220">Razor kullanarak bir görünüm şablon dosyası oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-220">You create a view template file using Razor.</span></span> <span data-ttu-id="b2b95-221">Jilet tabanlı görünüm şablonlarında *.cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b2b95-222">C# ile HTML çıktısı oluşturmak için zarif bir yol sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="b2b95-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b2b95-223">Şu `Index` anda yöntem denetleyici sınıfında sabit kodlanmış bir ileti ile bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="b2b95-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b2b95-224">`HelloWorldController` Sınıfta, `Index` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b2b95-225">Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b2b95-226">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b2b95-227">Denetleyici yöntemleri *(eylem yöntemleri*olarak da `Index` bilinir), yukarıdaki yöntem <xref:Microsoft.AspNetCore.Mvc.IActionResult> gibi, genellikle <xref:Microsoft.AspNetCore.Mvc.ActionResult>bir (veya `string`türetilen bir sınıf), gibi bir tür değil döndürür .</span><span class="sxs-lookup"><span data-stu-id="b2b95-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b2b95-228">Görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="b2b95-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2b95-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2b95-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2b95-230">*Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.</span><span class="sxs-lookup"><span data-stu-id="b2b95-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b2b95-231">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **yeni > öğe ekleyin.**</span><span class="sxs-lookup"><span data-stu-id="b2b95-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b2b95-232">Yeni **Öğe Ekle - MvcMovie** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="b2b95-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b2b95-233">Sağ üstteki arama kutusuna, *görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="b2b95-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b2b95-234">**Jilet Görünümü'nü** seçin</span><span class="sxs-lookup"><span data-stu-id="b2b95-234">Select **Razor View**</span></span>

  * <span data-ttu-id="b2b95-235">**Ad** kutusu değerini tutun, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b2b95-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b2b95-236">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="b2b95-236">Select **Add**</span></span>

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2b95-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2b95-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2b95-239">Için `Index` bir görünüm `HelloWorldController`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b2b95-240">*Görünümler/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b2b95-241">*Görünümler/HelloWorld* klasör adı *Index.cshtml'e*yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2b95-242">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2b95-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2b95-243">*Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.</span><span class="sxs-lookup"><span data-stu-id="b2b95-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b2b95-244">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **Yeni > Dosya Ekle'** ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b2b95-245">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="b2b95-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b2b95-246">Sol bölmede **Web'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="b2b95-247">Orta bölmede **Boş HTML dosyasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="b2b95-248">**Ad** kutusuna *Index.cshtml* yazın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="b2b95-249">**Yeni'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-249">Select **New**.</span></span>

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="b2b95-251">*Görünümler/HelloWorld/Index.cshtml* Razor görünüm dosyasının içeriğini aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b2b95-252">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b2b95-253">Yöntem `Index` çok `HelloWorldController` yapmadı; bu yöntem `return View();`tarayıcıya bir yanıt işlemek için bir görünüm şablondosyası kullanması gerektiğini belirten deyimi koştu.</span><span class="sxs-lookup"><span data-stu-id="b2b95-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b2b95-254">Görünüm şablonu dosya adı belirtilmedi, MVC varsayılan görünüm dosyasını kullanmaya varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="b2b95-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b2b95-255">Varsayılan görünüm dosyası yöntemle aynı ada sahiptir (`Index`), bu nedenle */Views/HelloWorld/Index.cshtml* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b2b95-256">Aşağıdaki resimde "Görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b2b95-257">görünümünde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="b2b95-257">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b2b95-259">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b2b95-259">Change views and layout pages</span></span>

<span data-ttu-id="b2b95-260">Menü bağlantılarını seçin **(MvcMovie**, **Home**, ve **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="b2b95-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b2b95-261">Her sayfa aynı menü düzenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="b2b95-262">Menü düzeni *Görünümler/Paylaşılan/_Layout.cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b2b95-263">*Görünümler/Paylaşılan/_Layout.cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b2b95-264">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b2b95-265">`@RenderBody()` Hattı bul.</span><span class="sxs-lookup"><span data-stu-id="b2b95-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b2b95-266">`RenderBody`oluşturduğunuz tüm görünüme özel sayfaların düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b2b95-267">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Ev/Gizlilik.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b2b95-268">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b2b95-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="b2b95-269">Başlık ve altbilgi öğelerinde, `Movie App`'' olarak değiştirin `MvcMovie`</span><span class="sxs-lookup"><span data-stu-id="b2b95-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b2b95-270">Bağlantı elemanını `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`' ile değiştirme</span><span class="sxs-lookup"><span data-stu-id="b2b95-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b2b95-271">Aşağıdaki biçimlendirme vurgulanan değişiklikleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="b2b95-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="b2b95-272">Önceki biçimlendirmede, `asp-area` bu uygulama [Alanlar'ı](xref:mvc/controllers/areas)kullanmadığından, bağlantı etiketi [Yardımcı özelliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) atlandı.</span><span class="sxs-lookup"><span data-stu-id="b2b95-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="b2b95-273">**Not**: `Movies` Denetleyici uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="b2b95-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b2b95-274">Bu noktada, `Movie App` bağlantı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b2b95-275">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b2b95-276">Tarayıcı sekmesindeki başlığın **Gizlilik Politikası 'nı** nasıl görüntülenebilene dikkat edin - Gizlilik Politikası yerine Film Uygulaması **- Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="b2b95-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="b2b95-278">Ana **Sayfa** bağlantısını seçin ve başlık ve bağlantı metninin de **Film Uygulamasını**görüntülediğini fark edin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b2b95-279">Biz düzen şablonu bir kez değişiklik yapmak ve sitedeki tüm sayfaları yeni bağlantı metni ve yeni başlık yansıtacak başardık.</span><span class="sxs-lookup"><span data-stu-id="b2b95-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b2b95-280">*Görünümler/_ViewStart.cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b2b95-281">*Views/_ViewStart.cshtml* dosyası, her görünüme *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını getirir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b2b95-282">Özellik, `Layout` farklı bir düzen görünümü ayarlamak veya hiçbir `null` düzen dosyasının kullanılmaması için ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b2b95-283">`<h2>` *Görünümler/HelloWorld/Index.cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b2b95-284">Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod parçasının ekranı değiştirdiğini görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b2b95-285">`ViewData["Title"] = "Movie List";`yukarıdaki kodda sözlüğün `Title` `ViewData` özelliğini "Film Listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b2b95-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b2b95-286">Özellik, `Title` düzen sayfasındaki `<title>` HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b2b95-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b2b95-287">Değişikliği kaydedin `https://localhost:{PORT}/HelloWorld`ve ''ye gidin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b2b95-288">Tarayıcı başlığının, birincil başlığın ve ikincil başlıkların değiştiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b2b95-289">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülenmiş olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b2b95-290">Sunucudan gelen yanıtı yüklemeye zorlamak için tarayıcınızda Ctrl+F5 tuşuna basın.) Tarayıcı `ViewData["Title"]` *başlığı, Index.cshtml* görünüm şablonuna ve düzen dosyasına eklenen ek "- Film Uygulaması" ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b2b95-291">*Ayrıca Index.cshtml* görünüm şablonundaki içeriğin *Görünümler/Paylaşılan/_Layout.cshtml* görünüm şablonuyla nasıl birleştirilmeye ve tarayıcıya tek bir HTML yanıtı gönderildiğine de dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="b2b95-292">Düzen şablonları, uygulamanızdaki tüm sayfalarda geçerli olan değişiklikleri yapmayı gerçekten kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="b2b95-293">Daha fazla bilgi için [Bkz. Düzen](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="b2b95-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Film Listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b2b95-295">Bizim küçük "veri" (bu durumda "Merhaba Bizim Görünüm Şablonu!"</span><span class="sxs-lookup"><span data-stu-id="b2b95-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b2b95-296">mesaj) olsa da, sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="b2b95-296">message) is hard-coded, though.</span></span> <span data-ttu-id="b2b95-297">MVC uygulaması "V" (görünüm) ve bir "C" (denetleyici) var, ama hiçbir "M" (model) henüz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b2b95-298">Denetleyiciden Görünüme Veri Aktarma</span><span class="sxs-lookup"><span data-stu-id="b2b95-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b2b95-299">Denetleyici eylemleri, gelen bir URL isteğine yanıt olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b2b95-300">Denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b2b95-301">Denetleyici bir veri kaynağından veri alır ve tarayıcıya geri göndermek için ne tür bir yanıt karar verir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b2b95-302">Görünüm şablonları tarayıcıya bir HTML yanıtı oluşturmak ve biçimlendirmek için bir denetleyiciden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b2b95-303">Denetleyiciler, bir görünüm şablonuna yanıt verebilmesi için gereken verileri sağlamakla yükümlüdür.</span><span class="sxs-lookup"><span data-stu-id="b2b95-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b2b95-304">En iyi yöntem: Görünüm şablonları iş mantığı **gerçekleştirmemeli** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b2b95-305">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sağlanan verilerle çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b2b95-306">Bu "endişeleri ayırma" bakımı, kodun temiz, sınanabilir ve korunabilir tutulmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b2b95-307">Şu `Welcome` anda, `HelloWorldController` sınıftaki `name` yöntem `ID` bir ve bir parametre alır ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b2b95-308">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, bunun yerine görünüm şablonu kullanmak için denetleyiciyi değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b2b95-309">Görünüm şablonu dinamik bir yanıt oluşturur, bu da yanıtı oluşturmak için uygun veri bitlerinin denetleyiciden görünüme geçirilmesi gerektiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b2b95-310">Denetleyicinin görünüm şablonuna ihtiyaç duyduğu dinamik verileri (parametreleri) görünüm şablonuna erişebileceği bir `ViewData` sözlükte koyarak bunu yapın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b2b95-311">*HelloWorldController.cs,* sözlük `Welcome` için bir `Message` ve `NumTimes` değer `ViewData` eklemek için yöntemi değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b2b95-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b2b95-312">Sözlük `ViewData` dinamik bir nesnedir, bu da herhangi bir türün kullanılabildiği anlamına gelir; nesnenin içine bir şey koyana `ViewData` kadar tanımlı özellikleri yoktur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b2b95-313">[MVC model bağlama sistemi,](xref:mvc/models/model-binding) adres çubuğundaki `numTimes`sorgu dizesinden adı geçen parametreleri (ve)`name` yönteminizdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="b2b95-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b2b95-314">Tam *HelloWorldController.cs* dosyası aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="b2b95-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b2b95-315">Sözlük `ViewData` nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b2b95-316">*Görünümler/HelloWorld/Welcome.cshtml*adlı hoş geldiniz görünümü şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b2b95-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b2b95-317">*Welcome.cshtml* görünüm şablonunda "Merhaba" `NumTimes`görüntülenen bir döngü oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="b2b95-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b2b95-318">*Views/HelloWorld/Welcome.cshtml* içeriğini aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b2b95-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b2b95-319">Değişikliklerinizi kaydedin ve aşağıdaki URL'ye göz atın:</span><span class="sxs-lookup"><span data-stu-id="b2b95-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b2b95-320">Veriler URL'den alınır ve [MVC model bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye aktarılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b2b95-321">Denetleyici verileri bir `ViewData` sözlük tesidikkate alır ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b2b95-322">Görünüm daha sonra verileri tarayıcıya HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="b2b95-322">The view then renders the data as HTML to the browser.</span></span>

![Hoş geldiniz etiketini ve Hello Rick ifadesini gösteren gizlilik görünümü dört kez gösterilir](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b2b95-324">Yukarıdaki örnekte, `ViewData` sözlük denetleyiciden bir görünüme veri aktarmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b2b95-325">Daha sonra öğreticide, bir görünüm modeli denetleyiciden görünüme veri aktarmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b2b95-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b2b95-326">Veri aktarılabilmek için görünüm modeli yaklaşımı `ViewData` genellikle sözlük yaklaşımına göre daha çok tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="b2b95-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b2b95-327">Daha fazla bilgi [için ViewBag, ViewData veya TempData'nın ne zaman kullanılacağına](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) bakın.</span><span class="sxs-lookup"><span data-stu-id="b2b95-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b2b95-328">Bir sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b2b95-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b2b95-329">[Önceki](adding-controller.md)
> [Sonraki](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b2b95-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
