---
title: ASP.NET Core MVC'de Görünümler
author: ardalis
description: ASP.NET Core MVC'de görünümlerin uygulamanın veri sunumlarını ve kullanıcı etkileşimini nasıl ele ASP.NET öğrenin.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384076"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="245dc-103">ASP.NET Core MVC'de Görünümler</span><span class="sxs-lookup"><span data-stu-id="245dc-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="245dc-104">Yazar: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="245dc-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="245dc-105">Bu belge, ASP.NET Core MVC uygulamalarında kullanılan görünümleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="245dc-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="245dc-106">Jilet Sayfaları hakkında bilgi için, [Bkz. Jilet Sayfalarına Giriş](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="245dc-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="245dc-107">Model-View-Controller (MVC) deseninde, *görünüm* uygulamanın veri sunusunu ve kullanıcı etkileşimini işler.</span><span class="sxs-lookup"><span data-stu-id="245dc-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="245dc-108">Görünüm, katıştırılmış [Razor biçimlendirmesi](xref:mvc/views/razor)içeren bir HTML şablonudur.</span><span class="sxs-lookup"><span data-stu-id="245dc-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="245dc-109">Jilet biçimlendirme, istemciye gönderilen bir web sayfası oluşturmak için HTML biçimlendirmesi ile etkileşimedebilen koddur.</span><span class="sxs-lookup"><span data-stu-id="245dc-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="245dc-110">Core MVCASP.NET görünümler, Razor biçimlendirmede [C# programlama dilini](/dotnet/csharp/) kullanan *.cshtml* dosyalarıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="245dc-111">Genellikle, görünüm dosyaları uygulamanın denetleyicilerinin her biri için adlandırılmış [klasörlere](xref:mvc/controllers/actions)gruplandırılmış.</span><span class="sxs-lookup"><span data-stu-id="245dc-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="245dc-112">Klasörler uygulamanın kökündeki *görünümler* klasöründe depolanır:</span><span class="sxs-lookup"><span data-stu-id="245dc-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Visual Studio'nun Solution Explorer'ındaki Görünümler klasörü, About.cshtml, Contact.cshtml ve Index.cshtml dosyalarını göstermek için Açık Ana klasörüyle açılır](overview/_static/views_solution_explorer.png)

<span data-ttu-id="245dc-114">*Giriş* denetleyicisi, *Görünümler* klasörü içinde bir *Giriş* klasörüyle temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="245dc-115">*Ana klasör,* *Hakkında,* *İletişim*ve *Dizin* (ana sayfa) web sayfalarının görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="245dc-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="245dc-116">Bir kullanıcı bu üç web sayfasından birini istediğinde, *Ana Sayfa* denetleyicisindeki denetleyici eylemleri, bir web sayfasını oluşturmak ve kullanıcıya döndürmek için hangi üç görünümden hangisinin kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="245dc-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="245dc-117">Tutarlı web sayfası bölümleri sağlamak ve kod tekrarını azaltmak için [mizanpajları](xref:mvc/views/layout) kullanın.</span><span class="sxs-lookup"><span data-stu-id="245dc-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="245dc-118">Düzenlergenellikle üstbilgi, gezinti ve menü öğeleri ni ve altbilgiyi içerir.</span><span class="sxs-lookup"><span data-stu-id="245dc-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="245dc-119">Üstbilgi ve altbilgi genellikle birçok meta veri öğesi ve komut dosyası ve stil varlıklarına bağlantılar için ortak biçimlendirme içerir.</span><span class="sxs-lookup"><span data-stu-id="245dc-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="245dc-120">Düzenler, görünümlerinizdeki bu ortak plaka biçimlendirmeyi önlemenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="245dc-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="245dc-121">[Kısmi görünümler,](xref:mvc/views/partial) yeniden kullanılabilir görünüm bölümlerini yöneterek kod yinelemesini azaltır.</span><span class="sxs-lookup"><span data-stu-id="245dc-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="245dc-122">Örneğin, kısmi bir görünüm, bir blog web sitesinde çeşitli görünümlerde görünen bir yazar biyografisi için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="245dc-123">Yazar biyografisi sıradan görünüm içeriğidir ve web sayfasının içeriğini oluşturmak için kod gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="245dc-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="245dc-124">Yazar biyografi içeriği yalnızca model bağlama tarafından görünümü kullanılabilir, bu nedenle bu tür içerik için kısmi bir görünüm kullanmak idealdir.</span><span class="sxs-lookup"><span data-stu-id="245dc-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="245dc-125">[Görünüm bileşenleri,](xref:mvc/views/view-components) yinelenen kodu azaltmanıza olanak tanıyan kısmi görünümlere benzer, ancak web sayfasını işlemek için kodun sunucuda çalıştırılması nı gerektiren içeriği görüntülemek için uygundur.</span><span class="sxs-lookup"><span data-stu-id="245dc-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="245dc-126">Görüntü bileşenleri, işlenen içerik bir web sitesi alışveriş sepeti gibi veritabanı etkileşimi gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="245dc-127">Görünüm bileşenleri, web sayfası çıktısı üretmek için model bağlamayla sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="245dc-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="245dc-128">Görünümleri kullanmanın yararları</span><span class="sxs-lookup"><span data-stu-id="245dc-128">Benefits of using views</span></span>

<span data-ttu-id="245dc-129">Görünümler, kullanıcı arabirimi biçimlendirmesini uygulamanın diğer bölümlerinden ayırarak bir MVC uygulaması içindeki [endişelerin ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) belirlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="245dc-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="245dc-130">SoC tasarımını takip etmek, uygulamanızı modüler hale getirir ve bu da çeşitli avantajlar sağlar:</span><span class="sxs-lookup"><span data-stu-id="245dc-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="245dc-131">Uygulamanın bakımı daha kolaydır, çünkü daha iyi düzenlenir.</span><span class="sxs-lookup"><span data-stu-id="245dc-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="245dc-132">Görünümler genellikle uygulama özelliğine göre gruplandırılır.</span><span class="sxs-lookup"><span data-stu-id="245dc-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="245dc-133">Bu, bir özellik üzerinde çalışırken ilgili görünümleri bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="245dc-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="245dc-134">Uygulamanın bölümleri gevşek bir şekilde birleştirilmiş.</span><span class="sxs-lookup"><span data-stu-id="245dc-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="245dc-135">Uygulamanın görünümlerini iş mantığından ve veri erişim bileşenlerinden ayrı olarak oluşturabilir ve güncelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="245dc-136">Uygulamanın diğer bölümlerini güncellemek zorunda kalmadan uygulamanın görünümlerini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="245dc-137">Görünümler ayrı birimler olduğundan uygulamanın kullanıcı arabirimi bölümlerini sınamak daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="245dc-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="245dc-138">Daha iyi bir organizasyon sayesinde, kullanıcı arabiriminin bölümlerini yanlışlıkla yineleme olasılığınız daha düşüktür.</span><span class="sxs-lookup"><span data-stu-id="245dc-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="245dc-139">Görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="245dc-139">Creating a view</span></span>

<span data-ttu-id="245dc-140">Denetleyiciye özgü görünümler *Görünümler/[ControllerName]* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="245dc-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="245dc-141">Denetleyiciler arasında paylaşılan görünümler *Görünümler/Paylaşılanlar* klasörüne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="245dc-142">Bir görünüm oluşturmak için yeni bir dosya ekleyin ve *.cshtml* dosya uzantısı ile ilişkili denetleyici eylemiyle aynı adı verin.</span><span class="sxs-lookup"><span data-stu-id="245dc-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="245dc-143">*Giriş* denetleyicisinde *About* eylemiile karşılık gelen bir görünüm oluşturmak için *Görünümler/Ev* klasöründe *About.cshtml* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="245dc-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="245dc-144">*Jilet* leişareti `@` sembolle başlar.</span><span class="sxs-lookup"><span data-stu-id="245dc-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="245dc-145">Kıvırcık ayraçlar tarafından belirlenen [Razor kod blokları](xref:mvc/views/razor#razor-code-blocks) içine C# kodunu yerleştirerek C# ifadelerini çalıştırın (`{ ... }`).</span><span class="sxs-lookup"><span data-stu-id="245dc-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="245dc-146">Örneğin, yukarıda gösterildiği "Hakkında" `ViewData["Title"]` atamabakın.</span><span class="sxs-lookup"><span data-stu-id="245dc-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="245dc-147">Yalnızca `@` sembolle değere başvurarak HTML içindeki değerleri görüntüleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="245dc-148">Yukarıdaki ve `<h2>` `<h3>` öğelerin içeriğini görün.</span><span class="sxs-lookup"><span data-stu-id="245dc-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="245dc-149">Yukarıda gösterilen görüntüleme içeriği, kullanıcıya işlenen tüm web sayfasının yalnızca bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="245dc-150">Sayfanın düzeninin geri kalanı ve görünümün diğer ortak yönleri diğer görünüm dosyalarında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="245dc-151">Daha fazla bilgi edinmek için [Düzen konusuna](xref:mvc/views/layout)bakın.</span><span class="sxs-lookup"><span data-stu-id="245dc-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="245dc-152">Denetleyiciler görünümleri nasıl belirtir?</span><span class="sxs-lookup"><span data-stu-id="245dc-152">How controllers specify views</span></span>

<span data-ttu-id="245dc-153">Görünümler genellikle [eylemlerden](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)Bir Eylem Sonucu türü olan [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult)olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="245dc-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="245dc-154">Eylem yönteminiz doğrudan bir `ViewResult` yöntem oluşturabilir ve döndürebilir, ancak bu genellikle yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="245dc-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="245dc-155">Denetleyiciden devralınan çoğu denetleyici [olduğundan,](/dotnet/api/microsoft.aspnetcore.mvc.controller)aşağıdakileri döndürmek için `View` yardımcı yöntemini kullanmanız `ViewResult`yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="245dc-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="245dc-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="245dc-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="245dc-157">Bu eylem geri döndüğünde, son bölümde gösterilen *About.cshtml* görünümü aşağıdaki web sayfası olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="245dc-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Edge tarayıcısında işlenen sayfa hakkında](overview/_static/about-page.png)

<span data-ttu-id="245dc-159">`View` Yardımcı yöntemi birkaç aşırı yükleme vardır.</span><span class="sxs-lookup"><span data-stu-id="245dc-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="245dc-160">İsteğe bağlı olarak belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="245dc-160">You can optionally specify:</span></span>

* <span data-ttu-id="245dc-161">İade etmek için açık bir görünüm:</span><span class="sxs-lookup"><span data-stu-id="245dc-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="245dc-162">Görünüme geçmek için bir [model:](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="245dc-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="245dc-163">Hem görünüm hem de model:</span><span class="sxs-lookup"><span data-stu-id="245dc-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="245dc-164">Keşfi görüntüleme</span><span class="sxs-lookup"><span data-stu-id="245dc-164">View discovery</span></span>

<span data-ttu-id="245dc-165">Bir eylem bir görünüm döndürdüğünde, *görünüm bulma* adı verilen bir işlem gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="245dc-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="245dc-166">Bu işlem, görünüm adına göre hangi görünüm dosyasının kullanılacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="245dc-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="245dc-167">`View` Yöntemin varsayılan davranışı`return View();`( ) çağrıldığı eylem yöntemiyle aynı ada sahip bir görünüm döndürmektir.</span><span class="sxs-lookup"><span data-stu-id="245dc-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="245dc-168">Örneğin, Denetleyicinin *About* `ActionResult` yöntem adı *About.cshtml*adlı bir görünüm dosyası aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="245dc-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="245dc-169">İlk olarak, çalışma süresi görünüm için *Görünümler/[ControllerName]* klasöründe görünür.</span><span class="sxs-lookup"><span data-stu-id="245dc-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="245dc-170">Burada eşleşen bir görünüm bulamazsa, görünüm için *Paylaşılan* klasörü arar.</span><span class="sxs-lookup"><span data-stu-id="245dc-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="245dc-171">Görünümün `ViewResult` adını `return View();` `View` `return View("<ViewName>");`'' ile birlikte veya açıkça metoduna geçirmeniz önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="245dc-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="245dc-172">Her iki durumda da, bu sırada eşleşen bir görünüm dosyası için bulma aramalarını görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="245dc-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="245dc-173">*Görünümler/\[ControllerName]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="245dc-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="245dc-174">*Görünümler/Paylaşılan/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="245dc-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="245dc-175">Görünüm adı yerine görünüm dosyası yolu sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="245dc-176">Uygulama kökünden başlayan mutlak bir yol kullanıyorsanız (isteğe bağlı olarak "/" veya "~/" ile başlıyorsanız), *.cshtml* uzantısı belirtilmelidir:</span><span class="sxs-lookup"><span data-stu-id="245dc-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="245dc-177">*.cshtml* uzantısı olmadan farklı dizinlerde görünümleri belirtmek için göreli bir yol da kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="245dc-178">`HomeController`İçinde, göreli bir yol ile *Yönet* görünümlerinizin *Dizin* görünümünü döndürebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="245dc-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="245dc-179">Benzer şekilde, "./" öneki yle geçerli denetleyiciye özgü dizini belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="245dc-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="245dc-180">[Kısmi görünümler](xref:mvc/views/partial) ve [görünüm bileşenleri](xref:mvc/views/view-components) benzer (ancak aynı olmayan) bulma mekanizmaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="245dc-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="245dc-181">Özel bir [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)kullanarak görünümlerin uygulama içinde nasıl bulunduğuna ilişkin varsayılan kuralı özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="245dc-182">Görünüm bulma, görünüm dosyalarını dosya adına göre bulma üzerine dayanır.</span><span class="sxs-lookup"><span data-stu-id="245dc-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="245dc-183">Temel dosya sistemi büyük/küçük harf duyarlıysa, görünüm adları büyük olasılıkla büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="245dc-184">İşletim sistemleri arasında uyumluluk için, denetleyici ve eylem adları ile ilişkili görünüm klasörleri ve dosya adları arasındaki büyük/küçük harf le eşleştirin.</span><span class="sxs-lookup"><span data-stu-id="245dc-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="245dc-185">Büyük/küçük harf duyarlı bir dosya sistemiyle çalışırken bir görünüm dosyasının bulunamayacağı bir hatayla karşılaşırsanız, istenen görünüm dosyası ile gerçek görünüm dosyası adı arasındaki kasanın eşleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="245dc-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="245dc-186">Denetimörler, eylemler ve görünümler arasındaki ilişkileri yansıtacak şekilde görünümleriniz için dosya yapısını düzenlemenin en iyi uygulamasını izleyin.</span><span class="sxs-lookup"><span data-stu-id="245dc-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="245dc-187">Verileri görünümlere aktarma</span><span class="sxs-lookup"><span data-stu-id="245dc-187">Passing data to views</span></span>

<span data-ttu-id="245dc-188">Verileri çeşitli yaklaşımlar kullanarak görünümlere aktarın:</span><span class="sxs-lookup"><span data-stu-id="245dc-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="245dc-189">Güçlü bir şekilde yazılan veriler: viewmodel</span><span class="sxs-lookup"><span data-stu-id="245dc-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="245dc-190">Zayıf yazılan veriler</span><span class="sxs-lookup"><span data-stu-id="245dc-190">Weakly typed data</span></span>
  * <span data-ttu-id="245dc-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="245dc-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="245dc-192">Güçlü bir şekilde yazılan veriler (görünüm modeli)</span><span class="sxs-lookup"><span data-stu-id="245dc-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="245dc-193">En sağlam yaklaşım, görünümde bir [model](xref:mvc/models/model-binding) türü belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="245dc-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="245dc-194">Bu model genellikle bir *görünüm modeli*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="245dc-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="245dc-195">Görünüm modeli türünün bir örneğini eylemden görünüme geçirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="245dc-196">Verileri bir görünüme aktarmak için bir görünüm modeli kullanmak, görünümün *güçlü* tür denetiminden yararlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="245dc-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="245dc-197">*Güçlü yazma* (veya *güçlü bir şekilde yazılmış)* her değişken ve sabitin `string` `int`açıkça `DateTime`tanımlanmış bir türü olduğu anlamına gelir (örneğin, , , veya).</span><span class="sxs-lookup"><span data-stu-id="245dc-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="245dc-198">Görünümde kullanılan türlerin geçerliliği derleme zamanında denetlenir.</span><span class="sxs-lookup"><span data-stu-id="245dc-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="245dc-199">[Visual Studio](https://visualstudio.microsoft.com) ve [Visual Studio Code](https://code.visualstudio.com/) listesi güçlü [IntelliSense](/visualstudio/ide/using-intellisense)adlı bir özellik kullanarak sınıf üyeleri yazdı.</span><span class="sxs-lookup"><span data-stu-id="245dc-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="245dc-200">Bir görünüm modelinin özelliklerini görmek istediğinizde, bir dönem (`.`.</span><span class="sxs-lookup"><span data-stu-id="245dc-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="245dc-201">Bu, daha az hatayla kodu daha hızlı yazmanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="245dc-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="245dc-202">Yönergeyi kullanarak `@model` bir model belirtin.</span><span class="sxs-lookup"><span data-stu-id="245dc-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="245dc-203">Aşağıdakilerle `@Model`modeli kullanın:</span><span class="sxs-lookup"><span data-stu-id="245dc-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="245dc-204">Modeli görünüme sağlamak için denetleyici modeli bir parametre olarak geçirir:</span><span class="sxs-lookup"><span data-stu-id="245dc-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="245dc-205">Görünüme sağlayabileceğiniz model türleri üzerinde herhangi bir kısıtlama yoktur.</span><span class="sxs-lookup"><span data-stu-id="245dc-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="245dc-206">Çok az davranış (yöntem) tanımlı Düz Eski CLR Nesnesi (POCO) görünüm modellerini kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="245dc-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="245dc-207">Genellikle, görünüm modeli sınıfları *Modeller* klasöründe veya uygulamanın kökünde ayrı bir *Görünüm Modelleri* klasöründe depolanır.</span><span class="sxs-lookup"><span data-stu-id="245dc-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="245dc-208">Yukarıdaki örnekte kullanılan *Adres* görünüm modeli *Address.cs*adlı bir dosyada depolanan bir POCO görünüm modelidir:</span><span class="sxs-lookup"><span data-stu-id="245dc-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="245dc-209">Hiçbir şey hem görünüm model türleri niz hem de iş modeli türleriniz için aynı sınıfları kullanmanızı engellemez.</span><span class="sxs-lookup"><span data-stu-id="245dc-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="245dc-210">Ancak, ayrı modeller kullanmak, görünümlerinizin iş mantığından ve uygulamanızın veri erişim bölümlerinden bağımsız olarak değişmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="245dc-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="245dc-211">Modellerin ve görünüm modellerinin ayrılması, modeller de kullanıcı tarafından uygulamaya gönderilen veriler için [model bağlama](xref:mvc/models/model-binding) ve [doğrulama](xref:mvc/models/validation) kullandığında güvenlik avantajları sunar.</span><span class="sxs-lookup"><span data-stu-id="245dc-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="245dc-212">Zayıf yazılan veriler (ViewData, ViewData özniteliği ve ViewBag)</span><span class="sxs-lookup"><span data-stu-id="245dc-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="245dc-213">`ViewBag`*Razor Pages'da kullanılamaz.*</span><span class="sxs-lookup"><span data-stu-id="245dc-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="245dc-214">Güçlü bir şekilde yazılan görünümlere ek olarak, görünümler *zayıf yazılan* *(gevşek olarak yazılan)* veri koleksiyonuna erişebilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="245dc-215">Güçlü türlerin aksine, *zayıf türler* (veya gevşek *türler),* kullandığınız veri türünü açıkça bildirmediğiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="245dc-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="245dc-216">Denetleyicilere ve görünümlere küçük miktarlarda veri aktarmak için zayıf yazılan verilerin toplanmasını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="245dc-217">Arasında veri geçirme ...</span><span class="sxs-lookup"><span data-stu-id="245dc-217">Passing data between a ...</span></span>                        | <span data-ttu-id="245dc-218">Örnek</span><span class="sxs-lookup"><span data-stu-id="245dc-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="245dc-219">Denetleyici ve görünüm</span><span class="sxs-lookup"><span data-stu-id="245dc-219">Controller and a view</span></span>                             | <span data-ttu-id="245dc-220">Bir açılır listeyi verilerle doldurma.</span><span class="sxs-lookup"><span data-stu-id="245dc-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="245dc-221">Görünüm ve [düzen görünümü](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="245dc-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="245dc-222">\*\* \<Bir\*\* görünüm dosyasından düzen görünümünde başlık>öğesi içeriğini ayarlama.</span><span class="sxs-lookup"><span data-stu-id="245dc-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="245dc-223">[Kısmi görünüm](xref:mvc/views/partial) ve görünüm</span><span class="sxs-lookup"><span data-stu-id="245dc-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="245dc-224">Kullanıcının istediği web sayfasına göre verileri görüntüleyen bir widget.</span><span class="sxs-lookup"><span data-stu-id="245dc-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="245dc-225">Bu koleksiyon, denetleyiciler `ViewData` ve `ViewBag` görünümler üzerindeki özellikler veya bu koleksiyon aracılığıyla başvurulabilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="245dc-226">Özellik, `ViewData` zayıf yazılan nesnelerin sözlüğüdür.</span><span class="sxs-lookup"><span data-stu-id="245dc-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="245dc-227">Özellik, `ViewBag` altta yatan `ViewData` `ViewData` koleksiyon için dinamik özellikler sağlayan bir sarmalayıcıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="245dc-228">Not: Anahtar aramaları her ikisi için `ViewData` `ViewBag`de büyük/küçük harf duyarsızdır ve .</span><span class="sxs-lookup"><span data-stu-id="245dc-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="245dc-229">`ViewData`ve `ViewBag` çalışma zamanında dinamik olarak çözülür.</span><span class="sxs-lookup"><span data-stu-id="245dc-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="245dc-230">Derleme zamanı türü denetimi sunmadıkları için, her ikisi de genellikle bir görünüm modeli kullanmaktan daha fazla hataya eğilimlidir.</span><span class="sxs-lookup"><span data-stu-id="245dc-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="245dc-231">Bu nedenle, bazı geliştiriciler minimal veya `ViewData` hiç `ViewBag`kullanmayı tercih ve.</span><span class="sxs-lookup"><span data-stu-id="245dc-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="245dc-232">**Viewdata**</span><span class="sxs-lookup"><span data-stu-id="245dc-232">**ViewData**</span></span>

<span data-ttu-id="245dc-233">`ViewData`anahtarlar aracılığıyla `string` erişilen bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="245dc-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="245dc-234">Dize verileri döküme gerek kalmadan doğrudan depolanabilir ve doğrudan `ViewData` kullanılabilir, ancak bunları ayıklarken diğer nesne değerlerini belirli türlere dökmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="245dc-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="245dc-235">Denetimlerden `ViewData` görünümlere ve [kısmi görünümler](xref:mvc/views/partial) ve [düzenler](xref:mvc/views/layout)de dahil olmak üzere görünümler içinde veri aktarmak için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="245dc-236">Bir eylemde kullanarak `ViewData` bir karşılama ve adres için değerleri ayarlayan bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="245dc-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="245dc-237">Görünümdeki verilerle çalışın:</span><span class="sxs-lookup"><span data-stu-id="245dc-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="245dc-238">**ViewData özniteliği**</span><span class="sxs-lookup"><span data-stu-id="245dc-238">**ViewData attribute**</span></span>

<span data-ttu-id="245dc-239">[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) kullanan bir diğer yaklaşım [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="245dc-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="245dc-240">Öznitelik ile `[ViewData]` işaretlenmiş denetleyiciler veya Razor Page modellerindeki özelliklerin değerleri sözlükten depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="245dc-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="245dc-241">Aşağıdaki örnekte, Ev denetleyicisi `Title` ile `[ViewData]`işaretlenmiş bir özellik içerir.</span><span class="sxs-lookup"><span data-stu-id="245dc-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="245dc-242">Yöntem `About` Hakkında görünümü için başlığı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="245dc-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="245dc-243">Düzende, başlık ViewData sözlüğünden okunur:</span><span class="sxs-lookup"><span data-stu-id="245dc-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="245dc-244">**Görünüm Çantası**</span><span class="sxs-lookup"><span data-stu-id="245dc-244">**ViewBag**</span></span>

<span data-ttu-id="245dc-245">`ViewBag`*Razor Pages'da kullanılamaz.*</span><span class="sxs-lookup"><span data-stu-id="245dc-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="245dc-246">`ViewBag`'de depolanan nesnelere dinamik erişim sağlayan bir `ViewData` [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="245dc-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="245dc-247">`ViewBag`döküm gerektirmediğinden, çalışmak için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="245dc-248">Aşağıdaki örnek, yukarıdaki `ViewBag` gibi `ViewData` aynı sonuçla nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="245dc-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="245dc-249">**ViewData ve ViewBag'i aynı anda kullanma**</span><span class="sxs-lookup"><span data-stu-id="245dc-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="245dc-250">`ViewBag`*Razor Pages'da kullanılamaz.*</span><span class="sxs-lookup"><span data-stu-id="245dc-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="245dc-251">Bu `ViewData` `ViewBag` yana ve aynı `ViewData` temel toplama bakın, hem `ViewData` ve ve karıştırmak ve `ViewBag` aralarında maç zaman okuma ve yazma değerleri.</span><span class="sxs-lookup"><span data-stu-id="245dc-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="245dc-252">*About.cshtml* görünümünün `ViewData` üst kısmındaki başlığı ve açıklamayı kullanarak `ViewBag` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="245dc-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="245dc-253">Özellikleri okuyun ama kullanımı `ViewData` ters `ViewBag`ve .</span><span class="sxs-lookup"><span data-stu-id="245dc-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="245dc-254">*_Layout.cshtml* dosyasında, başlığı kullanarak `ViewData` edinin ve `ViewBag`açıklamayı kullanarak edinin:</span><span class="sxs-lookup"><span data-stu-id="245dc-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="245dc-255">Dizeleri için `ViewData`bir döküm gerektirmez unutmayın.</span><span class="sxs-lookup"><span data-stu-id="245dc-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="245dc-256">Döküm olmadan `@ViewData["Title"]` kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="245dc-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="245dc-257">Her `ViewData` ikisini `ViewBag` de ve aynı anda kullanmak, karıştırma ve eşleştirme okuma ve özellikleri yazma gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="245dc-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="245dc-258">Aşağıdaki biçimlendirme işlenir:</span><span class="sxs-lookup"><span data-stu-id="245dc-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="245dc-259">**ViewData ve ViewBag arasındaki farkların özeti**</span><span class="sxs-lookup"><span data-stu-id="245dc-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="245dc-260">`ViewBag`Razor Pages'da kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="245dc-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="245dc-261">[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)türetilmiştir , bu yüzden yararlı olabilir sözlük `ContainsKey`özellikleri `Add` `Remove`vardır, `Clear`gibi , , , ve .</span><span class="sxs-lookup"><span data-stu-id="245dc-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="245dc-262">Sözlükteki anahtarlar dizeleri, bu nedenle beyazboşluk izin verilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="245dc-263">Örnek: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="245dc-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="245dc-264">A dışındaki herhangi `string` bir tür kullanmak `ViewData`için görünümde döküm olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="245dc-265">[DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)türetilmiştir , böylece nokta gösterimi kullanarak dinamik özellikleri`@ViewBag.SomeKey = <value or object>`oluşturulmasını sağlar ( ), ve hiçbir döküm gereklidir.</span><span class="sxs-lookup"><span data-stu-id="245dc-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="245dc-266">Sözdizimi `ViewBag` denetleyicilere ve görünümlere eklemeyi daha hızlı yapar.</span><span class="sxs-lookup"><span data-stu-id="245dc-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="245dc-267">Null değerlerini denetlemek daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="245dc-267">Simpler to check for null values.</span></span> <span data-ttu-id="245dc-268">Örnek: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="245dc-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="245dc-269">**ViewData veya ViewBag ne zaman kullanılır?**</span><span class="sxs-lookup"><span data-stu-id="245dc-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="245dc-270">Her `ViewData` `ViewBag` ikisi de ve denetleyicileri ve görünümleri arasında az miktarda veri aktarmak için eşit derecede geçerli yaklaşımlardır.</span><span class="sxs-lookup"><span data-stu-id="245dc-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="245dc-271">Hangisinin kullanılacağı tercihi tercihe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="245dc-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="245dc-272">Ancak, tutarlı `ViewData` bir `ViewBag` şekilde kullanılan bir yaklaşımla kodun okunması ve bakımı daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="245dc-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="245dc-273">Her iki yaklaşım da çalışma zamanında dinamik olarak çözülür ve böylece çalışma zamanı hatalarına neden olur.</span><span class="sxs-lookup"><span data-stu-id="245dc-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="245dc-274">Bazı geliştirme ekipleri bunlardan kaçınır.</span><span class="sxs-lookup"><span data-stu-id="245dc-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="245dc-275">Dinamik görünümler</span><span class="sxs-lookup"><span data-stu-id="245dc-275">Dynamic views</span></span>

<span data-ttu-id="245dc-276">Bir model türünü kullanarak `@model` bildirmeyan ancak bir model örneği onlara geçirilen `return View(Address);`görünümler (örneğin,) örneğin özelliklerine dinamik olarak başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="245dc-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="245dc-277">Bu özellik esneklik sunar, ancak derleme koruması veya IntelliSense sunmaz.</span><span class="sxs-lookup"><span data-stu-id="245dc-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="245dc-278">Özellik yoksa, web sayfası oluşturma çalışma zamanında başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="245dc-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="245dc-279">Daha fazla görünüm özellikleri</span><span class="sxs-lookup"><span data-stu-id="245dc-279">More view features</span></span>

<span data-ttu-id="245dc-280">[Tag Yardımcıları,](xref:mvc/views/tag-helpers/intro) varolan HTML etiketlerine sunucu tarafı davranışı eklemeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="245dc-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="245dc-281">Tag Helpers'ı kullanmak, görünümleriniz içinde özel kod veya yardımcı yazma gereksinimini önler.</span><span class="sxs-lookup"><span data-stu-id="245dc-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="245dc-282">Etiket yardımcıları HTML öğelerine öznitelikleri olarak uygulanır ve bunları işleyen düzenleyiciler tarafından yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="245dc-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="245dc-283">Bu, çeşitli araçlarda görünüm biçimlendirmesini oluşturmanıza ve oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="245dc-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="245dc-284">Birçok yerleşik HTML Yardımcısı ile özel HTML biçimlendirmesi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="245dc-285">Daha karmaşık kullanıcı arabirimi mantığı [Görünüm Bileşenleri](xref:mvc/views/view-components)tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="245dc-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="245dc-286">Görünüm bileşenleri, denetleyicilerin ve görünümlerin sunduğu aynı SoC'yi sağlar.</span><span class="sxs-lookup"><span data-stu-id="245dc-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="245dc-287">Ortak kullanıcı arabirimi öğeleri tarafından kullanılan verilerle ilgili eylem ve görünüm gereksinimini ortadan kaldırabilirler.</span><span class="sxs-lookup"><span data-stu-id="245dc-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="245dc-288">ASP.NET Core'un diğer pek çok yönü gibi, görünümler de [bağımlılık enjeksiyonuna](xref:fundamentals/dependency-injection)destek vererek, hizmetlerin [görünümlere enjekte edilmesine](xref:mvc/views/dependency-injection)olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="245dc-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
