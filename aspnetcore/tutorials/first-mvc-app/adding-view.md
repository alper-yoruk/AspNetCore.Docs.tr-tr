---
title: ASP.NET Core MVC uygulamasına görünüm ekleme
author: rick-anderson
description: Basit bir ASP.NET Core MVC uygulamasına görünüm ekleme
ms.author: riande
ms.date: 8/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 233d6cc060481c7b2efc9a1babc12f60f457a075
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507341"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="79d8e-103">ASP.NET Core MVC uygulamasına görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="79d8e-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="79d8e-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="79d8e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79d8e-105">Bu bölümde, `HelloWorldController` [Razor](xref:mvc/views/razor) BIR istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için, sınıfını görünüm dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="79d8e-106">Kullanarak bir görünüm şablonu dosyası oluşturursunuz Razor .</span><span class="sxs-lookup"><span data-stu-id="79d8e-106">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="79d8e-107">tabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-107">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="79d8e-108">C# ile HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="79d8e-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="79d8e-109">Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="79d8e-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="79d8e-110">Sınıfında, `HelloWorldController` `Index` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="79d8e-111">Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="79d8e-112">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="79d8e-113">Yukarıdaki yöntem gibi denetleyici Yöntemleri ( *eylem yöntemleri*olarak da bilinir), `Index` genellikle <xref:Microsoft.AspNetCore.Mvc.IActionResult> <xref:Microsoft.AspNetCore.Mvc.ActionResult> gibi bir tür değil, genellikle bir (veya öğesinden türetilmiş bir sınıf) döndürür `string` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="79d8e-114">Görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="79d8e-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79d8e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79d8e-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="79d8e-116">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="79d8e-117">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="79d8e-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="79d8e-118">**Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="79d8e-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="79d8e-119">Sağ üst köşedeki arama kutusuna *Görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="79d8e-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="79d8e-120">\*\* Razor Görünüm\*\* Seç</span><span class="sxs-lookup"><span data-stu-id="79d8e-120">Select **Razor View**</span></span>

  * <span data-ttu-id="79d8e-121">*Index. cshtml* **adlı ad** kutusu değerini saklayın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="79d8e-122">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="79d8e-122">Select **Add**</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="79d8e-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="79d8e-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="79d8e-125">İçin bir `Index` görünüm ekleyin `HelloWorldController` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="79d8e-126">*Views/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="79d8e-127">*Views/HelloWorld* klasör adı *Index. cshtml*dosyasına yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="79d8e-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79d8e-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="79d8e-129">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="79d8e-130">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="79d8e-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="79d8e-131">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="79d8e-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="79d8e-132">Sol bölmedeki **ASP .NET Core** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="79d8e-133">Orta bölmedeki **MVC görünümü sayfasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="79d8e-134">**Ad** kutusuna *Index* yazın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="79d8e-135">**Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-135">Select **New**.</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="79d8e-137">*Views/HelloWorld/Index. cshtml* Razor Görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="79d8e-138">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79d8e-139">`Index`' Deki yöntemi `HelloWorldController` çok fazla değil `return View();` , yönteminin tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten ifadesini çalıştırdı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="79d8e-140">Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış.</span><span class="sxs-lookup"><span data-stu-id="79d8e-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="79d8e-141">Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir ( `Index` ), bu nedenle */views/HelloWorld/Index.cshtml* içindeki görünüm şablonu kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="79d8e-142">Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir</span><span class="sxs-lookup"><span data-stu-id="79d8e-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="79d8e-143">görünümde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="79d8e-143">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="79d8e-145">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-145">Change views and layout pages</span></span>

<span data-ttu-id="79d8e-146">Menü bağlantılarını (**Mvcmovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="79d8e-147">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="79d8e-148">Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="79d8e-149">*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="79d8e-150">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="79d8e-151">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="79d8e-152">`RenderBody`, oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="79d8e-153">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="79d8e-154">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="79d8e-155">*Görünümler/paylaşılan/_Layout. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="79d8e-156">Değişiklikler vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="79d8e-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="79d8e-157">Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yaptı:</span><span class="sxs-lookup"><span data-stu-id="79d8e-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="79d8e-158">' nin ' `MvcMovie` öğesine tekrarı `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="79d8e-159">Öğesine bağla öğesi `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="79d8e-160">Önceki biçimlendirmede, `asp-area=""` Bu uygulama [alan](xref:mvc/controllers/areas)kullandığından [tutturucu etiketi Yardımcısı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ve öznitelik değeri atlandı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="79d8e-161">**Not**: `Movies` Denetleyici uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="79d8e-162">Bu noktada `Movie App` bağlantı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="79d8e-163">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="79d8e-164">Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi**:</span><span class="sxs-lookup"><span data-stu-id="79d8e-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="79d8e-166">**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını**da görüntülediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="79d8e-167">Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="79d8e-168">*Views/_ViewStart. cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="79d8e-169">*Views/_ViewStart. cshtml* dosyası her bir görünüm için *views/Shared/_Layout. cshtml* dosyasını getirir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="79d8e-170">`Layout`Özelliği, farklı bir düzen görünümü ayarlamak veya bir `null` Düzen dosyası kullanılmayacak şekilde ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="79d8e-171">`<h2>` *Views/HelloWorld/Index. cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="79d8e-172">Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod bitini görüntülemeyi değiştirmekten daha fazla bilgi alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="79d8e-173">`ViewData["Title"] = "Movie List";`Yukarıdaki kodda, `Title` `ViewData` sözlüğün özelliğini "film listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79d8e-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="79d8e-174">`Title`Özelliği, `<title>` Düzen sayfasındaki HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="79d8e-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="79d8e-175">Değişikliği kaydedin ve öğesine gidin `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79d8e-176">Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="79d8e-177">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="79d8e-178">Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Tarayıcı başlığı `ViewData["Title"]` *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımızda oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="79d8e-179">*Index. cshtml* görünüm şablonundaki içerik *views/Shared/_Layout. cshtml* görünüm şablonuyla birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="79d8e-180">Tarayıcıya tek bir HTML yanıtı gönderilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="79d8e-181">Düzen şablonları, bir uygulamadaki tüm sayfalara uygulanan değişiklikler yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="79d8e-182">Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="79d8e-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="79d8e-184">"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az.</span><span class="sxs-lookup"><span data-stu-id="79d8e-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="79d8e-185">ileti) sabit kodludur, ancak.</span><span class="sxs-lookup"><span data-stu-id="79d8e-185">message) is hard-coded, though.</span></span> <span data-ttu-id="79d8e-186">MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.</span><span class="sxs-lookup"><span data-stu-id="79d8e-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="79d8e-187">Denetleyiciden görünüme veri geçirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="79d8e-188">Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="79d8e-189">Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="79d8e-190">Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="79d8e-191">Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="79d8e-192">Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="79d8e-193">En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="79d8e-194">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="79d8e-195">Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="79d8e-196">Şu anda, `Welcome` `HelloWorldController` sınıfındaki yöntemi bir `name` ve parametresi alır `ID` ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="79d8e-197">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="79d8e-198">Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="79d8e-199">Bu, denetleyicinin görünüm şablonu tarafından daha sonra erişebileceği bir sözlükte görünüm şablonunun gerektirdiği dinamik verileri (parametreler) yerleştirerek bunu yapın `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="79d8e-200">*HelloWorldController.cs*içinde, `Welcome` sözlüğe bir ve değeri eklemek için yöntemini değiştirin `Message` `NumTimes` `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="79d8e-201">`ViewData`Sözlük dinamik bir nesnedir, yani herhangi bir tür kullanılabilir; `ViewData` nesnenin içine bir öğe yerleştirene kadar tanımlanmış bir özelliği yoktur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="79d8e-202">[MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri ( `name` ve `numTimes` ) adres çubuğundaki sorgu dizesinden, yöntemizdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="79d8e-203">Tüm *HelloWorldController.cs* dosyası şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="79d8e-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="79d8e-204">`ViewData`Sözlük nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="79d8e-205">*Görünümler/HelloWorld/Welcome. cshtml*adlı bir hoş geldiniz görünüm şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79d8e-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="79d8e-206">*Welcome. cshtml* görünüm şablonunda "Hello" öğesini görüntüleyen bir döngü oluşturacaksınız `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="79d8e-207">*Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="79d8e-208">Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="79d8e-209">Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="79d8e-210">Denetleyici, verileri bir `ViewData` Sözlükte paketler ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="79d8e-211">Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-211">The view then renders the data as HTML to the browser.</span></span>

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="79d8e-213">Yukarıdaki örnekte `ViewData` Sözlük, denetleyicideki verileri bir görünüme geçirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="79d8e-214">Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="79d8e-215">Veri geçirme yaklaşımına yönelik görünüm modeli, sözlük yaklaşımına göre genel olarak tercih edilir `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="79d8e-216">Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="79d8e-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="79d8e-217">Sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="79d8e-218">[Önceki](adding-controller.md) 
>  [Sonraki](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="79d8e-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79d8e-219">Bu bölümde, `HelloWorldController` [Razor](xref:mvc/views/razor) BIR istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için, sınıfını görünüm dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="79d8e-220">Kullanarak bir görünüm şablonu dosyası oluşturursunuz Razor .</span><span class="sxs-lookup"><span data-stu-id="79d8e-220">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="79d8e-221">tabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-221">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="79d8e-222">C# ile HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="79d8e-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="79d8e-223">Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="79d8e-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="79d8e-224">Sınıfında, `HelloWorldController` `Index` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="79d8e-225">Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="79d8e-226">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="79d8e-227">Yukarıdaki yöntem gibi denetleyici Yöntemleri ( *eylem yöntemleri*olarak da bilinir), `Index` genellikle <xref:Microsoft.AspNetCore.Mvc.IActionResult> <xref:Microsoft.AspNetCore.Mvc.ActionResult> gibi bir tür değil, genellikle bir (veya öğesinden türetilmiş bir sınıf) döndürür `string` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="79d8e-228">Görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="79d8e-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79d8e-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79d8e-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="79d8e-230">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="79d8e-231">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="79d8e-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="79d8e-232">**Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="79d8e-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="79d8e-233">Sağ üst köşedeki arama kutusuna *Görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="79d8e-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="79d8e-234">\*\* Razor Görünüm\*\* Seç</span><span class="sxs-lookup"><span data-stu-id="79d8e-234">Select **Razor View**</span></span>

  * <span data-ttu-id="79d8e-235">*Index. cshtml* **adlı ad** kutusu değerini saklayın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="79d8e-236">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="79d8e-236">Select **Add**</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="79d8e-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="79d8e-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="79d8e-239">İçin bir `Index` görünüm ekleyin `HelloWorldController` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="79d8e-240">*Views/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="79d8e-241">*Views/HelloWorld* klasör adı *Index. cshtml*dosyasına yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="79d8e-242">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79d8e-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="79d8e-243">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="79d8e-244">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="79d8e-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="79d8e-245">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="79d8e-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="79d8e-246">Sol bölmedeki **Web** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="79d8e-247">Orta bölmedeki **boş HTML dosyasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="79d8e-248">**Ad** kutusuna *Index. cshtml* yazın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="79d8e-249">**Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-249">Select **New**.</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="79d8e-251">*Views/HelloWorld/Index. cshtml* Razor Görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="79d8e-252">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79d8e-253">`Index`' Deki yöntemi `HelloWorldController` çok fazla değil `return View();` , yönteminin tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten ifadesini çalıştırdı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="79d8e-254">Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış.</span><span class="sxs-lookup"><span data-stu-id="79d8e-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="79d8e-255">Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir ( `Index` ), bu nedenle */views/HelloWorld/Index.cshtml* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="79d8e-256">Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir</span><span class="sxs-lookup"><span data-stu-id="79d8e-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="79d8e-257">görünümde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="79d8e-257">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="79d8e-259">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-259">Change views and layout pages</span></span>

<span data-ttu-id="79d8e-260">Menü bağlantılarını (**Mvcmovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="79d8e-261">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="79d8e-262">Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="79d8e-263">*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="79d8e-264">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="79d8e-265">Satırı bulun `@RenderBody()` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="79d8e-266">`RenderBody`, oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="79d8e-267">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="79d8e-268">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="79d8e-269">Başlık ve altbilgi öğelerinde öğesini `MvcMovie` olarak değiştirin `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="79d8e-270">Tutturucu öğesini olarak değiştirin `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="79d8e-271">Aşağıdaki biçimlendirme vurgulanan değişiklikleri göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="79d8e-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="79d8e-272">Önceki biçimlendirmede, `asp-area` Bu uygulama [alan](xref:mvc/controllers/areas)kullandığından [tutturucu etiketi yardımcı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) atlandı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="79d8e-273">**Not**: `Movies` Denetleyici uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="79d8e-274">Bu noktada `Movie App` bağlantı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="79d8e-275">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="79d8e-276">Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi**:</span><span class="sxs-lookup"><span data-stu-id="79d8e-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="79d8e-278">**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını**da görüntülediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="79d8e-279">Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="79d8e-280">*Views/_ViewStart. cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="79d8e-281">*Views/_ViewStart. cshtml* dosyası her bir görünüm için *views/Shared/_Layout. cshtml* dosyasını getirir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="79d8e-282">`Layout`Özelliği, farklı bir düzen görünümü ayarlamak veya bir `null` Düzen dosyası kullanılmayacak şekilde ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="79d8e-283">`<h2>` *Views/HelloWorld/Index. cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="79d8e-284">Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod bitini görüntülemeyi değiştirmekten daha fazla bilgi alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="79d8e-285">`ViewData["Title"] = "Movie List";`Yukarıdaki kodda, `Title` `ViewData` sözlüğün özelliğini "film listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79d8e-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="79d8e-286">`Title`Özelliği, `<title>` Düzen sayfasındaki HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="79d8e-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="79d8e-287">Değişikliği kaydedin ve öğesine gidin `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79d8e-288">Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="79d8e-289">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79d8e-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="79d8e-290">Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Tarayıcı başlığı `ViewData["Title"]` *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımızda oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="79d8e-291">Ayrıca, *Index. cshtml* görünüm şablonundaki içeriğin *Görünümler/paylaşılan/_Layout. cshtml* görünüm şablonuyla nasıl birleştirildiğini ve tarayıcıya tek bir HTML yanıtı gönderildiğini de unutmayın.</span><span class="sxs-lookup"><span data-stu-id="79d8e-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="79d8e-292">Düzen şablonları, uygulamanızdaki tüm sayfalara uygulanan değişiklikler yapmayı gerçekten kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="79d8e-293">Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="79d8e-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="79d8e-295">"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az.</span><span class="sxs-lookup"><span data-stu-id="79d8e-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="79d8e-296">ileti) sabit kodludur, ancak.</span><span class="sxs-lookup"><span data-stu-id="79d8e-296">message) is hard-coded, though.</span></span> <span data-ttu-id="79d8e-297">MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.</span><span class="sxs-lookup"><span data-stu-id="79d8e-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="79d8e-298">Denetleyiciden görünüme veri geçirme</span><span class="sxs-lookup"><span data-stu-id="79d8e-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="79d8e-299">Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="79d8e-300">Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="79d8e-301">Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="79d8e-302">Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="79d8e-303">Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="79d8e-304">En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="79d8e-305">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="79d8e-306">Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="79d8e-307">Şu anda, `Welcome` `HelloWorldController` sınıfındaki yöntemi bir `name` ve parametresi alır `ID` ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="79d8e-308">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="79d8e-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="79d8e-309">Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="79d8e-310">Bu, denetleyicinin görünüm şablonu tarafından daha sonra erişebileceği bir sözlükte görünüm şablonunun gerektirdiği dinamik verileri (parametreler) yerleştirerek bunu yapın `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="79d8e-311">*HelloWorldController.cs*içinde, `Welcome` sözlüğe bir ve değeri eklemek için yöntemini değiştirin `Message` `NumTimes` `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="79d8e-312">`ViewData`Sözlük dinamik bir nesnedir, yani herhangi bir tür kullanılabilir; `ViewData` nesnenin içine bir öğe yerleştirene kadar tanımlanmış bir özelliği yoktur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="79d8e-313">[MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri ( `name` ve `numTimes` ) adres çubuğundaki sorgu dizesinden, yöntemizdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="79d8e-314">Tüm *HelloWorldController.cs* dosyası şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="79d8e-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="79d8e-315">`ViewData`Sözlük nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="79d8e-316">*Görünümler/HelloWorld/Welcome. cshtml*adlı bir hoş geldiniz görünüm şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="79d8e-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="79d8e-317">*Welcome. cshtml* görünüm şablonunda "Hello" öğesini görüntüleyen bir döngü oluşturacaksınız `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="79d8e-318">*Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="79d8e-319">Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:</span><span class="sxs-lookup"><span data-stu-id="79d8e-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="79d8e-320">Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="79d8e-321">Denetleyici, verileri bir `ViewData` Sözlükte paketler ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="79d8e-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="79d8e-322">Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="79d8e-322">The view then renders the data as HTML to the browser.</span></span>

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="79d8e-324">Yukarıdaki örnekte `ViewData` Sözlük, denetleyicideki verileri bir görünüme geçirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="79d8e-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="79d8e-325">Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79d8e-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="79d8e-326">Veri geçirme yaklaşımına yönelik görünüm modeli, sözlük yaklaşımına göre genel olarak tercih edilir `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="79d8e-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="79d8e-327">Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="79d8e-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="79d8e-328">Sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79d8e-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="79d8e-329">[Önceki](adding-controller.md) 
>  [Sonraki](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="79d8e-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
