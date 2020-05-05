---
title: ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş
author: ardalis
description: ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye nasıl başlaleyeceğinizi öğrenin.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777052"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="4dc7f-103">ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş</span><span class="sxs-lookup"><span data-stu-id="4dc7f-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="4dc7f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)ve [Scott Ade](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="4dc7f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="4dc7f-105">Bu makalede, bir ASP.NET MVC projesini [ASP.NET Core MVC](../mvc/overview.md)'ye geçirmeye nasıl başlacağınız gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="4dc7f-106">Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="4dc7f-107">ASP.NET MVC 'den geçiş, birden çok adımlı bir işlemdir ve bu makalede ilk kurulum, temel denetleyiciler ve görünümler, statik içerik ve istemci tarafı bağımlılıkları ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="4dc7f-108">Ek makaleler, birçok ASP.NET MVC projesinde bulunan yapılandırma ve kimlik kodunun geçirilmesini kapsar.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="4dc7f-109">Örneklerdeki sürüm numaraları güncel olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="4dc7f-110">Projelerinizi uygun şekilde güncelleştirmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="4dc7f-111">Başlatıcı ASP.NET MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="4dc7f-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="4dc7f-112">Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="4dc7f-113">Ad alanı, bir sonraki adımda oluşturduğumuz ASP.NET Core projeyle eşleşmesi için *WebApp1* adıyla oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="4dc7f-116">*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5*arasında değiştirin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="4dc7f-117">Visual Studio yeni çözüm adını (*Mvc5*) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="4dc7f-118">ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="4dc7f-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="4dc7f-119">Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun (*WebApp1*), böylece iki projedeki ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="4dc7f-120">Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="4dc7f-121">Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="4dc7f-124">*Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="4dc7f-125">Projeyi *WebApp1*olarak adlandırın ve **bireysel kullanıcı hesaplarının**bir kimlik doğrulama seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="4dc7f-126">Bu uygulamayı *Fullaspnetcore*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="4dc7f-127">Bu projeyi oluşturmak, dönüştürmeye zaman kazandırır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="4dc7f-128">Son sonucu görmek veya kodu dönüştürme projesine kopyalamak için, şablon tarafından oluşturulan koda bakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="4dc7f-129">Ayrıca, şablon tarafından oluşturulan projeyle karşılaştırmak için bir dönüştürme adımında takılı olduğunuzda da yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="4dc7f-130">Siteyi MVC kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4dc7f-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="4dc7f-131">.NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="4dc7f-132">Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="4dc7f-133">.NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="4dc7f-134">.NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. All metapackage](xref:fundamentals/metapackage) öğesine başvurulur.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="4dc7f-135">Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="4dc7f-136">.NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="4dc7f-137">.NET Core veya .NET Framework hedeflenirken, MVC uygulamalarına göre yaygın olarak kullanılan paketler, proje dosyasında tek tek listelenir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="4dc7f-138">`Microsoft.AspNetCore.Mvc`ASP.NET Core MVC çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="4dc7f-139">`Microsoft.AspNetCore.StaticFiles`, statik dosya işleyicisidir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="4dc7f-140">ASP.NET Core çalışma zamanı Modüler olur ve statik dosyalara (bkz. [statik dosyaları](xref:fundamentals/static-files)) hizmeti sağlamak için açıkça oturum açmalısınız.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="4dc7f-141">*Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="4dc7f-142">`UseStaticFiles` Genişletme yöntemi statik dosya işleyicisini ekler.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="4dc7f-143">Daha önce belirtildiği gibi, ASP.NET çalışma zamanı Modüler olur ve statik dosyaları sağlamak için açıkça kabul etmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="4dc7f-144">`UseMvc` Uzantı yöntemi yönlendirme ekler.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="4dc7f-145">Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="4dc7f-146">Denetleyici ekleme ve görüntüleme</span><span class="sxs-lookup"><span data-stu-id="4dc7f-146">Add a controller and view</span></span>

<span data-ttu-id="4dc7f-147">Bu bölümde, sonraki bölümde geçirebileceğiniz ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="4dc7f-148">Bir *Controllers* klasörü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="4dc7f-149">*Controllers* klasörüne *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="4dc7f-151">Bir *Görünüm* klasörü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="4dc7f-152">Bir *Görünüm/giriş* klasörü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="4dc7f-153">*Görünümler/giriş* klasörüne *Index. cshtml* adlı bir \*\* Razor görünüm\*\* ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

<span data-ttu-id="4dc7f-155">Proje yapısı aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-155">The project structure is shown below:</span></span>

![WebApp1 dosyalarını ve klasörlerini gösteren Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="4dc7f-157">*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="4dc7f-158">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-158">Run the app.</span></span>

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

<span data-ttu-id="4dc7f-160">Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="4dc7f-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="4dc7f-161">Artık en az çalışma ASP.NET Core projesi olduğuna göre, işlevselliği ASP.NET MVC projesinden geçirmeye başlayabiliriz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="4dc7f-162">Aşağıdakileri taşıdık:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-162">We need to move the following:</span></span>

* <span data-ttu-id="4dc7f-163">istemci tarafı içerik (CSS, yazı tipleri ve betikler)</span><span class="sxs-lookup"><span data-stu-id="4dc7f-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="4dc7f-164">denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="4dc7f-164">controllers</span></span>

* <span data-ttu-id="4dc7f-165">görünümler</span><span class="sxs-lookup"><span data-stu-id="4dc7f-165">views</span></span>

* <span data-ttu-id="4dc7f-166">modeller</span><span class="sxs-lookup"><span data-stu-id="4dc7f-166">models</span></span>

* <span data-ttu-id="4dc7f-167">Paketleme</span><span class="sxs-lookup"><span data-stu-id="4dc7f-167">bundling</span></span>

* <span data-ttu-id="4dc7f-168">filtreler</span><span class="sxs-lookup"><span data-stu-id="4dc7f-168">filters</span></span>

* <span data-ttu-id="4dc7f-169">Oturum açma/kapatma Identity (Bu, sonraki öğreticide yapılır.)</span><span class="sxs-lookup"><span data-stu-id="4dc7f-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="4dc7f-170">Denetleyiciler ve görünümler</span><span class="sxs-lookup"><span data-stu-id="4dc7f-170">Controllers and views</span></span>

* <span data-ttu-id="4dc7f-171">Yöntemlerin her birini ASP.NET MVC `HomeController` 'den New `HomeController`öğesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="4dc7f-172">ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yönteminin dönüş türü [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET Core MVC 'de, eylem metotları bunun yerine `IActionResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="4dc7f-173">`ActionResult`uygular `IActionResult`, bu nedenle eylem yöntemlerinizi dönüş türünü değiştirmenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="4dc7f-174">ASP.NET MVC projesindeki *. cshtml*, *Contact. cshtml*ve *Index. cshtml* Razor görünüm dosyalarını ASP.NET Core projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="4dc7f-175">ASP.NET Core uygulamasını çalıştırın ve her yöntemi test edin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="4dc7f-176">Düzen dosyasını veya stilleri henüz geçirmedik, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="4dc7f-177">Ve görünümleri için Düzen dosyası oluşturulmuş bağlantılar yoktur, bu nedenle bunları tarayıcıdan çağırmanız gerekir (4492 değerini projenizde kullanılan bağlantı noktası numarasıyla değiştirin). **4492** `About` `Contact`</span><span class="sxs-lookup"><span data-stu-id="4dc7f-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Kişi sayfası](mvc/_static/contact-page.png)

<span data-ttu-id="4dc7f-179">Stil ve menü öğelerinin eksikliğine göz önünde.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="4dc7f-180">Sonraki bölümde bunu çözeceğiz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="4dc7f-181">Statik içerik</span><span class="sxs-lookup"><span data-stu-id="4dc7f-181">Static content</span></span>

<span data-ttu-id="4dc7f-182">ASP.NET MVC 'nin önceki sürümlerinde, statik içerik Web projesinin kökünden barındırılıyor ve sunucu tarafı dosyalarıyla karıştı.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="4dc7f-183">ASP.NET Core, statik içerik *Wwwroot* klasöründe barındırılır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="4dc7f-184">Eski ASP.NET MVC uygulamanızdan statik içeriği ASP.NET Core projenizdeki *Wwwroot* klasörüne kopyalamak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="4dc7f-185">Bu örnek dönüştürmede:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-185">In this sample conversion:</span></span>

* <span data-ttu-id="4dc7f-186">*Ayrıcalıklı Icon. ico* dosyasını eski MVC projesinden ASP.NET Core projesindeki *Wwwroot* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="4dc7f-187">Eski ASP.NET MVC projesi, stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* klasörlerinde depolar.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="4dc7f-188">Eski ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur (*Görünümler/paylaşılan/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="4dc7f-189">ASP.NET MVC projesindeki *Bootstrap. js* ve *Bootstrap. css* dosyalarını yeni projedeki *Wwwroot* klasörüne kopyalayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="4dc7f-190">Bunun yerine, sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="4dc7f-191">Düzen dosyasını geçirme</span><span class="sxs-lookup"><span data-stu-id="4dc7f-191">Migrate the layout file</span></span>

* <span data-ttu-id="4dc7f-192">*_ViewStart. cshtml* dosyasını eskı ASP.NET MVC projesinin *Görünümler* klasöründen ASP.NET Core projesinin *Görünümler* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="4dc7f-193">*_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="4dc7f-194">Bir *Görünüm/paylaşılan* klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="4dc7f-195">*Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* klasöründen ASP.NET Core projenin *views* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="4dc7f-196">*_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4dc7f-197">*_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="4dc7f-198">Etiket Yardımcıları yeni düzen dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="4dc7f-199">*_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="4dc7f-200">*_Layout. cshtml* dosyasını eskı ASP.NET MVC projesinin *Görünümler/paylaşılan* klasöründen ASP.NET Core projenin *Görünümler/paylaşılan* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="4dc7f-201">*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):</span><span class="sxs-lookup"><span data-stu-id="4dc7f-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="4dc7f-202">*Bootstrap. css* ' nin yükleneceği bir `@Styles.Render("~/Content/css")` `<link>` öğeyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="4dc7f-203">Kaldırın `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="4dc7f-204">`@Html.Partial("_LoginPartial")` Çizgiyi açıklama (çizgi ile `@*...*@`çevreleyin).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="4dc7f-205">Daha fazla bilgi için bkz. [kimlik Identity doğrulamasını geçirme ve ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="4dc7f-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="4dc7f-206">Bir `@Scripts.Render("~/bundles/jquery")` `<script>` öğesiyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="4dc7f-207">Bir `@Scripts.Render("~/bundles/bootstrap")` `<script>` öğesiyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="4dc7f-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="4dc7f-208">Önyükleme CSS ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="4dc7f-209">JQuery ve Bootstrap JavaScript ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="4dc7f-210">Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="4dc7f-211">Siteyi tarayıcıda görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-211">View the site in the browser.</span></span> <span data-ttu-id="4dc7f-212">Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="4dc7f-213">*Isteğe bağlı:* Yeni düzen dosyasını kullanmayı denemek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="4dc7f-214">Bu proje için, düzen dosyasını *Fullaspnetcore* projesinden kopyalayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="4dc7f-215">Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4dc7f-216">Paketlemeyi ve küçültmeye göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4dc7f-216">Configure bundling and minification</span></span>

<span data-ttu-id="4dc7f-217">Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](../client-side/bundling-and-minification.md)yönelik.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="4dc7f-218">HTTP 500 hatalarını çözme</span><span class="sxs-lookup"><span data-stu-id="4dc7f-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="4dc7f-219">Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="4dc7f-220">Örneğin, *views/_ViewImports. cshtml* dosyası projenizde mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası alırsınız.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="4dc7f-221">ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme*sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="4dc7f-222">Bu, aşağıdaki kodda ayrıntılı olarak verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4dc7f-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="4dc7f-223">ASP.NET Core, bir Web uygulamasındaki işlenmemiş özel durumları HTTP 500 hata yanıtlarına dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="4dc7f-224">Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="4dc7f-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="4dc7f-225">Daha fazla bilgi için bkz. [tanıtıcı hatalarında](../fundamentals/error-handling.md) **Geliştirici özel durum sayfasını kullanma** .</span><span class="sxs-lookup"><span data-stu-id="4dc7f-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4dc7f-226">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4dc7f-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
