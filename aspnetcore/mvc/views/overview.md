---
title: ASP.NET Core MVC 'deki görünümler
author: ardalis
description: Görünümlerin ASP.NET Core MVC 'de uygulamanın veri sunumunu ve kullanıcı etkileşimini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020879"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="6bfbb-103">ASP.NET Core MVC 'deki görünümler</span><span class="sxs-lookup"><span data-stu-id="6bfbb-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="6bfbb-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="6bfbb-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6bfbb-105">Bu belgede ASP.NET Core MVC uygulamalarında kullanılan görünümler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="6bfbb-106">Sayfalar hakkında bilgi için Razor bkz. [ Razor sayfalara giriş](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="6bfbb-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="6bfbb-107">Model-View-Controller (MVC) modelinde, *Görünüm* uygulamanın veri sunumunu ve kullanıcı etkileşimini işler.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="6bfbb-108">Görünüm, gömülü [ Razor biçimlendirme](xref:mvc/views/razor)içeren bir HTML şablonudur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="6bfbb-109">Razorbiçimlendirme, istemciye gönderilen bir Web sayfası oluşturmak için HTML işaretlemesi ile etkileşen koddur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="6bfbb-110">ASP.NET Core MVC 'de, görünümler, biçimlendirme içinde [C# programlama dilini](/dotnet/csharp/) kullanan *. cshtml* dosyalarıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="6bfbb-111">Genellikle, görünüm dosyaları uygulama [denetleyicilerinin](xref:mvc/controllers/actions)her biri için adlandırılmış klasörler halinde gruplandırılır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="6bfbb-112">Klasörler, uygulamanın kökündeki bir *Görünümler* klasöründe depolanır:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Visual Studio Çözüm Gezgini Görünüm klasörü,. cshtml, Contact. cshtml ve Index. cshtml dosyalarını göstermek için açık giriş klasörüyle açıktır](overview/_static/views_solution_explorer.png)

<span data-ttu-id="6bfbb-114">*Ana* denetleyici, *Görünümler* klasörünün içindeki bir *giriş* klasörüyle temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="6bfbb-115">*Giriş* klasörü, *hakkında*, *iletişim*ve *Dizin* (giriş sayfası) Web sayfalarının görünümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="6bfbb-116">Bir Kullanıcı bu üç Web sayfasından birini istediğinde, *ana* denetleyicideki denetleyici eylemleri, Kullanıcı için bir Web sayfası oluşturmak ve döndürmek için kullanılan üç görünümden hangisinin kullanıldığını tespit ediyor.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="6bfbb-117">Tutarlı Web sayfası bölümleri sağlamak ve kod tekrarlamayı azaltmak için [düzenleri](xref:mvc/views/layout) kullanın.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="6bfbb-118">Düzenler genellikle üstbilgiyi, gezinti ve menü öğelerini ve alt bilgisini içerir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="6bfbb-119">Üst bilgi ve altbilgi genellikle birçok meta veri öğesi için ortak biçimlendirme ve betik ve stil varlıklarına bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="6bfbb-120">Düzenler, görünümlerinizde bu ortak biçimlendirmeyi önlemenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="6bfbb-121">[Kısmi görünümler](xref:mvc/views/partial) , görünümlerin yeniden kullanılabilir parçalarını yöneterek kod yinelemeyi azaltır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="6bfbb-122">Örneğin, kısmi bir görünüm çeşitli görünümlerde görüntülenen bir blog web sitesinde yazar biyografı için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="6bfbb-123">Yazar biyografları sıradan görünüm içeriğine sahiptir ve Web sayfasının içeriğini üretmek için kodun yürütülmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="6bfbb-124">Yazar biyografları, tek başına model bağlama tarafından kullanılabilir, bu nedenle bu içerik türü için kısmi bir görünüm kullanmak idealdir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="6bfbb-125">[Görünüm bileşenleri](xref:mvc/views/view-components) , yinelenen kodu azaltmanıza izin veren kısmi görünümlere benzerdir, ancak Web sayfasını işlemek için kodun sunucuda çalıştırılmasını gerektiren içeriği görüntüleme için uygundur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="6bfbb-126">Görüntüleme bileşenleri, işlenen içerik bir Web sitesi alışveriş sepeti gibi veritabanı etkileşimi gerektirdiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="6bfbb-127">Web sayfası çıkışı oluşturmak için, görünüm bileşenlerini model bağlama ile sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="6bfbb-128">Görünümleri kullanmanın avantajları</span><span class="sxs-lookup"><span data-stu-id="6bfbb-128">Benefits of using views</span></span>

<span data-ttu-id="6bfbb-129">Görünümler, Kullanıcı arabirimi işaretlemesini uygulamanın diğer bölümlerinden ayırarak bir MVC uygulamasında [kaygıların ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) sağlamaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="6bfbb-130">Aşağıdaki SoC tasarımı, uygulamanızın modüler olmasını sağlayarak çeşitli avantajlar sağlar:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="6bfbb-131">Daha iyi bir şekilde düzenlendiğinden, uygulamanın bakımını daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="6bfbb-132">Görünümler genellikle uygulama özelliğine göre gruplandırılır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="6bfbb-133">Bu, bir özellik üzerinde çalışırken ilgili görünümleri bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="6bfbb-134">Uygulamanın parçaları gevşek olarak bağlanmış.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="6bfbb-135">Uygulamanın görünümlerini iş mantığı ve veri erişim bileşenlerinden ayrı olarak oluşturup güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="6bfbb-136">Uygulamanın diğer bölümlerini güncelleştirmek zorunda kalmadan uygulamanın görünümlerini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="6bfbb-137">Görünümler ayrı birimler olduğundan uygulamanın kullanıcı arabirimi parçalarını test etmek daha kolay.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="6bfbb-138">Daha iyi bir kuruluş nedeniyle, Kullanıcı arabiriminin bölümlerini yanlışlıkla tekrarlamanız daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="6bfbb-139">Görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="6bfbb-139">Creating a view</span></span>

<span data-ttu-id="6bfbb-140">Denetleyiciye özgü görünümler, *Görünümler/[ControllerName]* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="6bfbb-141">Denetleyiciler arasında paylaşılan görünümler *Görünümler/paylaşılan* klasörüne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="6bfbb-142">Bir görünüm oluşturmak için yeni bir dosya ekleyin ve *. cshtml* dosya uzantısıyla ilişkili denetleyici eylemiyle aynı adı verin.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="6bfbb-143">*Giriş* denetleyicisindeki *hakkında* eylemine karşılık gelen bir görünüm oluşturmak Için, *Görünümler/giriş* klasöründe bir *About. cshtml* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="6bfbb-144">*Razor* biçimlendirme `@` simgesiyle başlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="6bfbb-145">C# kodunu [ Razor kod blokları](xref:mvc/views/razor#razor-code-blocks) içine yerleştirerek, küme ayraçları () tarafından kapalı olarak ayarlayarak c# deyimlerini çalıştırın `{ ... }` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="6bfbb-146">Örneğin, yukarıda gösterilen "hakkında" atamasını inceleyin `ViewData["Title"]` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="6bfbb-147">Yalnızca simgesiyle birlikte değeri yazarak HTML içindeki değerleri görüntüleyebilirsiniz `@` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="6bfbb-148">`<h2>`Yukarıdaki ve öğelerinin içeriğine bakın `<h3>` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="6bfbb-149">Yukarıda gösterilen görünüm içeriği yalnızca kullanıcıya işlenmiş olan tüm Web sayfasının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="6bfbb-150">Sayfanın düzeninin geri kalanı ve görünümün diğer yaygın yönleri diğer görünüm dosyalarında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="6bfbb-151">Daha fazla bilgi için [Düzen konusuna](xref:mvc/views/layout)bakın.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="6bfbb-152">Denetleyiciler görünümleri nasıl belirler</span><span class="sxs-lookup"><span data-stu-id="6bfbb-152">How controllers specify views</span></span>

<span data-ttu-id="6bfbb-153">Görünümler genellikle eylemlerden bir [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)türü olan [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult)olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="6bfbb-154">Eylem yönteminiz doğrudan oluşturabilir ve döndürebilir `ViewResult` , ancak yaygın olarak yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="6bfbb-155">Çoğu [Denetleyici denetleyicisinden](/dotnet/api/microsoft.aspnetcore.mvc.controller)devraldığı için, yalnızca yardımcı yöntemini kullanarak şunu `View` döndürün `ViewResult` :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="6bfbb-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="6bfbb-157">Bu eylem döndüğünde, son bölümde gösterilen *hakkında. cshtml* görünümü aşağıdaki Web sayfası olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Edge tarayıcısında işlenen sayfa hakkında](overview/_static/about-page.png)

<span data-ttu-id="6bfbb-159">`View`Yardımcı yönteminde birkaç aşırı yükleme vardır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="6bfbb-160">İsteğe bağlı olarak şunları belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-160">You can optionally specify:</span></span>

* <span data-ttu-id="6bfbb-161">Döndürülecek açık bir görünüm:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="6bfbb-162">Görünüme geçirilecek bir [model](xref:mvc/models/model-binding) :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="6bfbb-163">Hem görünüm hem de model:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="6bfbb-164">Bulmayı görüntüle</span><span class="sxs-lookup"><span data-stu-id="6bfbb-164">View discovery</span></span>

<span data-ttu-id="6bfbb-165">Bir eylem bir görünüm döndürdüğünde, *görünüm bulma* adlı bir işlem gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="6bfbb-166">Bu işlem, görünüm adına göre hangi görünüm dosyasının kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="6bfbb-167">Yönteminin varsayılan davranışı `View` ( `return View();` ), çağrılan eylem yöntemiyle aynı ada sahip bir görünüm döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="6bfbb-168">Örneğin, denetleyicinin *hakkında* `ActionResult` yöntemi adı *. cshtml*adlı bir görünüm dosyasını aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="6bfbb-169">İlk olarak, çalışma zamanı görünümün *Görünümler/[ControllerName]* klasörüne bakar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="6bfbb-170">Burada eşleşen bir görünüm bulamazsa, görünümün *paylaşılan* klasörünü arar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="6bfbb-171">' İ örtülü olarak döndürmeniz ya da `ViewResult` `return View();` açıkça görünüm adını ile yöntemine iletmeniz önemi yoktur `View` `return View("<ViewName>");` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="6bfbb-172">Her iki durumda da, eşleşen bir görünüm dosyası için bulma aramalarını şu sırayla görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="6bfbb-173">*Görünümler/ \[ ControllerName]/ \[ ViewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="6bfbb-174">*Görünümler/paylaşılan/ \[ ViewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="6bfbb-175">Görünüm adı yerine bir görünüm dosyası yolu sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="6bfbb-176">Uygulama kökünde başlayan mutlak bir yol kullanılıyorsa (isteğe bağlı olarak "/" veya "~/" ile başlayan), *. cshtml* uzantısı belirtilmelidir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="6bfbb-177">Ayrıca, *. cshtml* uzantısı olmadan farklı dizinlerdeki görünümleri belirtmek için göreli bir yol da kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="6bfbb-178">İçinde `HomeController` , *Yönetim* görünümlerinizin *Dizin* görünümünü göreli bir yol ile döndürebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="6bfbb-179">Benzer şekilde, "./" önekiyle geçerli denetleyiciye özgü dizini belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="6bfbb-180">[Kısmi görünümler](xref:mvc/views/partial) ve [Görünüm bileşenleri](xref:mvc/views/view-components) benzer (ancak aynı) bulma mekanizmalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="6bfbb-181">Özel bir [ıviewlocationgenişleticisi](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)kullanarak, görünümler uygulama içinde nasıl konumlandırılabilir varsayılan kuralı özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="6bfbb-182">Görünüm bulma, dosya adına göre görünüm dosyalarını bulmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="6bfbb-183">Temeldeki dosya sistemi büyük/küçük harfe duyarlı ise, görünüm adları büyük olasılıkla büyük küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="6bfbb-184">İşletim sistemleri arasında uyumluluk için, denetleyici ve eylem adları ile ilişkili görünüm klasörleri ve dosya adları arasındaki büyük/küçük harfe eşleştirin.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="6bfbb-185">Büyük/küçük harfe duyarlı dosya sistemiyle çalışırken bir görünüm dosyasının bulunamadığını belirten bir hatayla karşılaşırsanız, istenen görünüm dosyası ile gerçek görünüm dosyası adı arasında büyük/küçük harf eşleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="6bfbb-186">Görünümlerinizin dosya yapısını, bakım ve açıklık için denetleyiciler, Eylemler ve görünümler arasındaki ilişkileri yansıtacak şekilde düzenleme konusunda en iyi yöntemi izleyin.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="6bfbb-187">Verileri görünümlere geçirme</span><span class="sxs-lookup"><span data-stu-id="6bfbb-187">Passing data to views</span></span>

<span data-ttu-id="6bfbb-188">Çeşitli yaklaşımlar kullanarak verileri görünümlere geçirin:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="6bfbb-189">Kesin türü belirtilmiş veri: ViewModel</span><span class="sxs-lookup"><span data-stu-id="6bfbb-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="6bfbb-190">Zayıf yazılmış veriler</span><span class="sxs-lookup"><span data-stu-id="6bfbb-190">Weakly typed data</span></span>
  * <span data-ttu-id="6bfbb-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="6bfbb-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="6bfbb-192">Kesin tür belirtilmiş veriler (ViewModel)</span><span class="sxs-lookup"><span data-stu-id="6bfbb-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="6bfbb-193">En güçlü yaklaşım, görünümde bir [model](xref:mvc/models/model-binding) türü belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="6bfbb-194">Bu model genellikle *ViewModel*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="6bfbb-195">ViewModel türünün bir örneğini eylemden görünüme geçirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="6bfbb-196">Görünümü bir görünüme aktarmak için ViewModel kullanmak, görünümün *tanımlayıcı* tür denetlemesinin avantajlarından yararlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="6bfbb-197">*Güçlü yazma* (veya *kesin belirlenmiş*), her değişken ve sabitin açıkça tanımlanmış bir tür (örneğin,, `string` veya) olduğu anlamına gelir `int` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="6bfbb-198">Bir görünümde kullanılan türlerin geçerliliği derleme zamanında denetlenir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="6bfbb-199">[Visual Studio](https://visualstudio.microsoft.com) ve [Visual Studio Code](https://code.visualstudio.com/) listesi [IntelliSense](/visualstudio/ide/using-intellisense)adlı bir özellik kullanılarak türü kesin belirlenmiş sınıf üyeleridir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="6bfbb-200">ViewModel özelliklerini görmek istediğinizde, ViewModel için değişken adını ve ardından bir nokta ( `.` ) yazın.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="6bfbb-201">Bu, daha az hata vererek kodu daha hızlı yazmanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="6bfbb-202">Yönergesini kullanarak bir model belirtin `@model` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="6bfbb-203">Modeli şu şekilde kullanın `@Model` :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="6bfbb-204">Modeli görünüme sağlamak için, denetleyici bu parametreyi bir parametre olarak geçirir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

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

<span data-ttu-id="6bfbb-205">Bir görünüme sağlayabilmeniz için model türlerinde hiçbir kısıtlama yoktur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="6bfbb-206">Basit eski CLR nesnesi (POCO) viewmodeller için çok az veya hiç davranış (Yöntem) tanımlanmış olarak kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="6bfbb-207">Genellikle ViewModel sınıfları, uygulamanın kökündeki *modeller* klasöründe veya ayrı bir *viewmodeller* klasöründe depolanır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="6bfbb-208">Yukarıdaki örnekte kullanılan *Adres* viewmodel, *Address.cs*adlı bir dosyada depolanan bir poco ViewModel modelidir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

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

<span data-ttu-id="6bfbb-209">Hiçbir şey, hem ViewModel türleriniz hem de iş modeli türleriniz için aynı sınıfları kullanmanızı önler.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="6bfbb-210">Ancak, ayrı modeller kullanmak görünümlerinizin, uygulamanızın iş mantığı ve veri erişimi bölümlerinden bağımsız olarak değişiklik yapmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="6bfbb-211">Modeller ve ViewModel ayrımı, modeller Kullanıcı tarafından uygulamaya gönderilen veriler için [model bağlama](xref:mvc/models/model-binding) ve [doğrulama](xref:mvc/models/validation) kullanırken de güvenlik avantajları sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="6bfbb-212">Zayıf yazılmış veriler (ViewData, ViewData özniteliği ve ViewBag)</span><span class="sxs-lookup"><span data-stu-id="6bfbb-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="6bfbb-213">`ViewBag`*içinde Razor kullanılamaz Sayfalar.*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="6bfbb-214">Türü kesin belirlenmiş görünümlere ek olarak, görünümler, verilerin bir *zayıf türü* olan ( *gevşek olarak yazılmış*) bir veri koleksiyonu erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="6bfbb-215">Güçlü türlerin aksine, *zayıf türler* (veya *gevşek türler*), kullanmakta olduğunuz veri türünü açıkça bildirmeyeceğiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="6bfbb-216">Denetleyicilerde ve görünümlerde küçük miktarlarda veri iletmek için zayıf yazılmış verilerin toplanmasını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="6bfbb-217">Verileri bir... arasında geçirme</span><span class="sxs-lookup"><span data-stu-id="6bfbb-217">Passing data between a ...</span></span>                        | <span data-ttu-id="6bfbb-218">Örnek</span><span class="sxs-lookup"><span data-stu-id="6bfbb-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="6bfbb-219">Denetleyici ve görünüm</span><span class="sxs-lookup"><span data-stu-id="6bfbb-219">Controller and a view</span></span>                             | <span data-ttu-id="6bfbb-220">Açılan listeyi verilerle doldurma.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="6bfbb-221">Görünüm ve [Düzen görünümü](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="6bfbb-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="6bfbb-222">**\<title>** Düzen görünümünde öğe içeriğini bir görünüm dosyasından ayarlama.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="6bfbb-223">[Kısmi görünüm](xref:mvc/views/partial) ve görünüm</span><span class="sxs-lookup"><span data-stu-id="6bfbb-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="6bfbb-224">Kullanıcı tarafından istenen web sayfasını temel alan verileri görüntüleyen pencere öğesi.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="6bfbb-225">Bu koleksiyona, `ViewData` denetleyiciler ve görünümlerde bulunan ya da `ViewBag` özellikleri aracılığıyla başvurulabilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="6bfbb-226">`ViewData`Özelliği, Zayıf yazılmış nesnelerin bir sözlüğüdür.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="6bfbb-227">`ViewBag`Özelliği, `ViewData` temel alınan koleksiyon için dinamik özellikler sağlayan etrafındaki bir sarmalayıcıdır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="6bfbb-228">Note: anahtar aramaları hem hem de için büyük/küçük harfe duyarsızdır `ViewData` `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="6bfbb-229">`ViewData`ve `ViewBag` , çalışma zamanında dinamik olarak çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="6bfbb-230">Derleme zamanı tür denetimi sunmadığı için, her ikisi de bir ViewModel kullanmaktan daha fazla hataya açıktır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="6bfbb-231">Bu nedenle, bazı geliştiriciler ve için en az bir süre önce ve hiç kullanmayın `ViewData` `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="6bfbb-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-232">**ViewData**</span></span>

<span data-ttu-id="6bfbb-233">`ViewData`Anahtarlar aracılığıyla erişilen bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) nesnesidir `string` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="6bfbb-234">Dize verileri doğrudan bir dönüştürme gerektirmeden depolanabilir ve kullanılabilir, ancak diğer `ViewData` nesne değerlerini ayıkladığınızda belirli türlere atamalısınız.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="6bfbb-235">`ViewData`Verileri denetleyicilerden görünümlere ve görünümler arasında ( [kısmi görünümler](xref:mvc/views/partial) ve [düzenler](xref:mvc/views/layout)dahil) geçirmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="6bfbb-236">Aşağıda bir selamlama ve bir adresin değerlerini bir eylemde kullanarak ayarlayan bir örnek verilmiştir `ViewData` :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

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

<span data-ttu-id="6bfbb-237">Bir görünümdeki verilerle çalışın:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-237">Work with the data in a view:</span></span>

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

<span data-ttu-id="6bfbb-238">**ViewData özniteliği**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-238">**ViewData attribute**</span></span>

<span data-ttu-id="6bfbb-239">[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) ' i kullanan başka bir yaklaşım da [viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="6bfbb-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="6bfbb-240">Ve Razor özniteliğiyle işaretlenmiş sayfa modelleriyle ilgili özellikler, `[ViewData]` değerlerinin depolandığı ve sözlükten yüklendiği.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="6bfbb-241">Aşağıdaki örnekte, giriş denetleyicisi `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="6bfbb-242">`About`Yöntemi, hakkında görünümü için başlığı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-242">The `About` method sets the title for the About view:</span></span>

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

<span data-ttu-id="6bfbb-243">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="6bfbb-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-244">**ViewBag**</span></span>

<span data-ttu-id="6bfbb-245">`ViewBag`*içinde Razor kullanılamaz Sayfalar.*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="6bfbb-246">`ViewBag`, içinde depolanan nesnelere dinamik erişim sağlayan bir [Dynamicviewdata](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) nesnesidir `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="6bfbb-247">`ViewBag`atama gerektirmediğinden, ile çalışmak daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="6bfbb-248">Aşağıdaki örnek, `ViewBag` Yukarıdaki ile aynı sonuçla nasıl kullanılacağını gösterir `ViewData` :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

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

<span data-ttu-id="6bfbb-249">**ViewData ve ViewBag 'i aynı anda kullanma**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="6bfbb-250">`ViewBag`*içinde Razor kullanılamaz Sayfalar.*</span><span class="sxs-lookup"><span data-stu-id="6bfbb-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="6bfbb-251">`ViewData` `ViewBag` Aynı temel koleksiyona başvuru yaptığından `ViewData` , `ViewData` ve `ViewBag` değerlerini okurken ve yazarken ve arasında karıştırma ve eşleştirme gibi her ikisini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="6bfbb-252">Using `ViewBag` `ViewData` *. cshtml* görünümünün üst kısmında kullanarak başlığı ve açıklamayı kullanarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="6bfbb-253">Özellikleri okuyun, ancak ve kullanımını tersine çevirin `ViewData` `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="6bfbb-254">*_Layout. cshtml* dosyasında, kullanarak başlığı alın `ViewData` ve kullanarak açıklamayı alın `ViewBag` :</span><span class="sxs-lookup"><span data-stu-id="6bfbb-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="6bfbb-255">Dizelerin için bir atama gerektirmediğini unutmayın `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="6bfbb-256">`@ViewData["Title"]`Atama olmadan kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="6bfbb-257">Hem hem `ViewData` de `ViewBag` aynı anda kullanılması, özellikleri karıştırarak ve eşleştirirken de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="6bfbb-258">Aşağıdaki biçimlendirme işlenir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="6bfbb-259">**ViewData ve ViewBag arasındaki farkların Özeti**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="6bfbb-260">`ViewBag`Razorsayfalarda yok.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="6bfbb-261">,, Ve gibi yararlı olabilecek sözlük özelliklerine sahip olan [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)öğesinden türetilir `ContainsKey` `Add` `Remove` `Clear` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="6bfbb-262">Sözlükteki anahtarlar dizelerdir, bu nedenle boşluğa izin verilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="6bfbb-263">Örnek: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="6bfbb-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="6bfbb-264">' Dan başka herhangi bir türün `string` kullanılacak görünümde tür ataması gerekir `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="6bfbb-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="6bfbb-265">[Dynamicviewdata](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)öğesinden türetilir, bu nedenle, nokta gösterimini () kullanarak dinamik özellikler oluşturulmasına izin verir `@ViewBag.SomeKey = <value or object>` ve hiçbir atama gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="6bfbb-266">Sözdizimi, `ViewBag` denetleyicilere ve görünümlere daha hızlı eklemenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="6bfbb-267">Null değerleri denetlemek için daha basittir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-267">Simpler to check for null values.</span></span> <span data-ttu-id="6bfbb-268">Örnek: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="6bfbb-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="6bfbb-269">**ViewData veya ViewBag ne zaman kullanılır?**</span><span class="sxs-lookup"><span data-stu-id="6bfbb-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="6bfbb-270">Her ikisi de `ViewData` `ViewBag` , denetleyiciler ve görünümler arasında küçük miktarlarda veri geçirilmesi için eşit ölçüde geçerli yaklaşımlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="6bfbb-271">Hangisinin kullanılacağı seçimi tercihi temel alır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="6bfbb-272">, Ve nesnelerini karıştırarak ve eşleştirebilir; `ViewData` `ViewBag` ancak, kod sürekli olarak kullanılan tek bir yaklaşımla daha kolay okunabilir ve devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="6bfbb-273">Her iki yaklaşım da çalışma zamanında dinamik olarak çözümlenir ve bu nedenle çalışma zamanı hatalarına neden olur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="6bfbb-274">Bazı geliştirme ekipleri bunlardan kaçınır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="6bfbb-275">Dinamik Görünümler</span><span class="sxs-lookup"><span data-stu-id="6bfbb-275">Dynamic views</span></span>

<span data-ttu-id="6bfbb-276">Kullanarak model türü bildirmeyen `@model` ancak bunlara bir model örneği geçirmeyen görünümler (örneğin, `return View(Address);` ), örnek özelliklerine dinamik olarak başvurabilir:</span><span class="sxs-lookup"><span data-stu-id="6bfbb-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="6bfbb-277">Bu özellik esneklik sunar, ancak derleme koruması veya IntelliSense sunmaz.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="6bfbb-278">Özellik yoksa, Web sayfası oluşturma çalışma zamanında başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="6bfbb-279">Daha fazla görünüm özelliği</span><span class="sxs-lookup"><span data-stu-id="6bfbb-279">More view features</span></span>

<span data-ttu-id="6bfbb-280">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , var olan HTML etiketlerine sunucu tarafı davranışı eklemenizi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="6bfbb-281">Etiket yardımcılarının kullanılması, görünümleriniz içinde özel kod veya yardımcılar yazma ihtiyacını önler.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="6bfbb-282">Etiket Yardımcıları HTML öğelerine öznitelikler olarak uygulanır ve bunları işleyeamayan düzenleyiciler tarafından yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="6bfbb-283">Bu, çeşitli araçlarındaki görünüm işaretlemesini düzenlemenizi ve işlemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="6bfbb-284">Özel HTML işaretlemesi oluşturmak birçok yerleşik HTML Yardımcıda sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="6bfbb-285">Daha karmaşık kullanıcı arabirimi mantığı, [Görünüm bileşenleri](xref:mvc/views/view-components)tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="6bfbb-286">Görüntüleme bileşenleri, denetleyiciler ve görünümler tarafından sunulan aynı SoC öğesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="6bfbb-287">Ortak kullanıcı arabirimi öğeleri tarafından kullanılan verilerle ilgili eylemler ve görünümler gereksinimini ortadan kaldırabilir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="6bfbb-288">ASP.NET Core diğer birçok yönü gibi, görünümler [bağımlılık ekleme](xref:fundamentals/dependency-injection)işlemini destekler ve hizmetlerin [görünümlere](xref:mvc/views/dependency-injection)eklenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="6bfbb-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
