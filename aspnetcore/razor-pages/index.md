---
title: RazorASP.NET Core sayfalara giriş
author: Rick-Anderson
description: RazorASP.NET Core sayfalarındaki sayfaların, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 52c3dc82e51cb4375954a603a1bfde60fd667b56
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103050"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="0373b-103">RazorASP.NET Core sayfalara giriş</span><span class="sxs-lookup"><span data-stu-id="0373b-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0373b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="0373b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="0373b-105">Sayfalar, denetleyici ve görünümleri kullanmaktan daha kolay ve daha üretken bir şekilde kodlama sayfasına odaklanmış senaryolar yapabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="0373b-106">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="0373b-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="0373b-107">Bu belge, sayfalara giriş sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="0373b-108">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="0373b-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="0373b-109">Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0373b-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="0373b-110">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="0373b-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0373b-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0373b-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0373b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0373b-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0373b-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0373b-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="0373b-115">RazorSayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0373b-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0373b-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0373b-117">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0373b-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0373b-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0373b-119">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0373b-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0373b-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0373b-121">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="0373b-122">Sayfaları</span><span class="sxs-lookup"><span data-stu-id="0373b-122"> Pages</span></span>

Razor<span data-ttu-id="0373b-123">*Startup.cs*'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="0373b-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="0373b-124">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="0373b-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="0373b-125">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="0373b-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="0373b-126">Bu, farklı kılan [`@page`](xref:mvc/views/razor#page) yönergedir.</span><span class="sxs-lookup"><span data-stu-id="0373b-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0373b-127">`@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0373b-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="0373b-128">`@page`sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0373b-129">`@page`diğer yapıların davranışını etkiler [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="0373b-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> Razor<span data-ttu-id="0373b-130">Sayfaların dosya adlarında *. cshtml* soneki vardır.</span><span class="sxs-lookup"><span data-stu-id="0373b-130"> Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="0373b-131">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0373b-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="0373b-132">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="0373b-133">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="0373b-134">Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0373b-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="0373b-135">Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="0373b-136">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="0373b-137">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="0373b-138">Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0373b-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="0373b-139">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="0373b-139">File name and path</span></span>               | <span data-ttu-id="0373b-140">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="0373b-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0373b-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="0373b-142">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="0373b-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="0373b-143">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="0373b-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="0373b-144">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="0373b-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="0373b-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="0373b-146">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="0373b-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="0373b-147">Notlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-147">Notes:</span></span>

* <span data-ttu-id="0373b-148">Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.</span><span class="sxs-lookup"><span data-stu-id="0373b-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="0373b-149">`Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="0373b-150">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="0373b-150">Write a basic form</span></span>

Razor<span data-ttu-id="0373b-151">Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="0373b-152">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="0373b-153">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0373b-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="0373b-154">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="0373b-155">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="0373b-156">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="0373b-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="0373b-157">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="0373b-158">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="0373b-159">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="0373b-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="0373b-160">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="0373b-161">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="0373b-162">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-162">This separation allows:</span></span>

* <span data-ttu-id="0373b-163">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="0373b-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="0373b-164">Birim testi</span><span class="sxs-lookup"><span data-stu-id="0373b-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="0373b-165">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="0373b-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="0373b-166">Herhangi bir HTTP fiili için işleyici metotları eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="0373b-167">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0373b-167">The most common handlers are:</span></span>

* <span data-ttu-id="0373b-168">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0373b-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="0373b-169">Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* Razor sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0373b-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="0373b-170">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="0373b-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="0373b-171">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="0373b-172">Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="0373b-173">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="0373b-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="0373b-174">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="0373b-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="0373b-175">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve sayfalarla aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="0373b-176">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="0373b-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="0373b-177">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0373b-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="0373b-178">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="0373b-178">Check for validation errors.</span></span>

* <span data-ttu-id="0373b-179">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="0373b-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="0373b-180">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="0373b-181">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="0373b-182">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="0373b-183">Sayfalardan işlenmiş HTML */Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0373b-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="0373b-184">Önceki kodda, formu deftere nakletme:</span><span class="sxs-lookup"><span data-stu-id="0373b-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="0373b-185">Geçerli verilerle:</span><span class="sxs-lookup"><span data-stu-id="0373b-185">With valid data:</span></span>

  * <span data-ttu-id="0373b-186">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="0373b-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="0373b-187">`RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="0373b-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="0373b-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="0373b-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="0373b-189">Bir eylem sonucudur.</span><span class="sxs-lookup"><span data-stu-id="0373b-189">Is an action result.</span></span>
    * <span data-ttu-id="0373b-190">, Veya ile `RedirectToAction` benzerdir `RedirectToRoute` (denetleyiciler ve görünümlerde kullanılır).</span><span class="sxs-lookup"><span data-stu-id="0373b-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="0373b-191">Sayfalar için özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-191">Is customized for pages.</span></span> <span data-ttu-id="0373b-192">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="0373b-193">`RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="0373b-194">Sunucuya geçirilen doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="0373b-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="0373b-195">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="0373b-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="0373b-196">`Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="0373b-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="0373b-197">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="0373b-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="0373b-198">`PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="0373b-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="0373b-199">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="0373b-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="0373b-200">Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="0373b-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="0373b-201">Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler.</span><span class="sxs-lookup"><span data-stu-id="0373b-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="0373b-202">Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="0373b-203">İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="0373b-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="0373b-204">Veriler sunucuya **nakledilmedi.**</span><span class="sxs-lookup"><span data-stu-id="0373b-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="0373b-205">İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="0373b-206">`Customer`Özelliği, [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) model bağlamasını kabul etmek için özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="0373b-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="0373b-207">`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="0373b-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="0373b-208">Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="0373b-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

Razor<span data-ttu-id="0373b-209">Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="0373b-209"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="0373b-210">Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0373b-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="0373b-211">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="0373b-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0373b-212">*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:</span><span class="sxs-lookup"><span data-stu-id="0373b-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="0373b-213">Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="0373b-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Etiket Yardımcıları kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0373b-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="0373b-215">Giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="0373b-215">The home page</span></span>

<span data-ttu-id="0373b-216">*Index. cshtml* giriş sayfasıdır:</span><span class="sxs-lookup"><span data-stu-id="0373b-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="0373b-217">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0373b-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0373b-218">*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="0373b-219">`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="0373b-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="0373b-220">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="0373b-221">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="0373b-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="0373b-222">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="0373b-223">*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="0373b-224">İşlenmiş HTML:</span><span class="sxs-lookup"><span data-stu-id="0373b-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="0373b-225">Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="0373b-226">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="0373b-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="0373b-227">, `handler` Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="0373b-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="0373b-228">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="0373b-229">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="0373b-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="0373b-230">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="0373b-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="0373b-231">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0373b-232">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0373b-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="0373b-233">`id`Sorgu dizesinden alır.</span><span class="sxs-lookup"><span data-stu-id="0373b-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="0373b-234">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="0373b-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="0373b-235">Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="0373b-236"><xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="0373b-237">Edit. cshtml dosyası</span><span class="sxs-lookup"><span data-stu-id="0373b-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="0373b-238">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="0373b-239">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="0373b-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="0373b-240">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="0373b-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="0373b-241">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="0373b-242">*Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="0373b-243">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="0373b-243">Validation</span></span>

<span data-ttu-id="0373b-244">Doğrulama kuralları:</span><span class="sxs-lookup"><span data-stu-id="0373b-244">Validation rules:</span></span>

* <span data-ttu-id="0373b-245">Model sınıfında bildirimli olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="0373b-246">Uygulamada her yerde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="0373b-247"><xref:System.ComponentModel.DataAnnotations>Ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="0373b-248">Dataaçıklamalarda, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.</span><span class="sxs-lookup"><span data-stu-id="0373b-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="0373b-249">Modeli göz önünde bulundurun `Customer` :</span><span class="sxs-lookup"><span data-stu-id="0373b-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="0373b-250">Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:</span><span class="sxs-lookup"><span data-stu-id="0373b-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="0373b-251">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0373b-251">The preceding code:</span></span>

* <span data-ttu-id="0373b-252">JQuery ve jQuery doğrulama betikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="0373b-253">`<div />`' İ `<span />` etkinleştirmek için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:</span><span class="sxs-lookup"><span data-stu-id="0373b-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="0373b-254">İstemci tarafı doğrulama.</span><span class="sxs-lookup"><span data-stu-id="0373b-254">Client-side validation.</span></span>
  * <span data-ttu-id="0373b-255">Doğrulama hatası işleme.</span><span class="sxs-lookup"><span data-stu-id="0373b-255">Validation error rendering.</span></span>

* <span data-ttu-id="0373b-256">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0373b-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="0373b-257">Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0373b-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="0373b-258">formunda.</span><span class="sxs-lookup"><span data-stu-id="0373b-258">on the form.</span></span> <span data-ttu-id="0373b-259">İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0373b-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="0373b-260">`[StringLength(10)]`Özniteliği `data-val-length-max="10"` işlenmiş html üzerinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0373b-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="0373b-261">`data-val-length-max`tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0373b-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="0373b-262">Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:</span><span class="sxs-lookup"><span data-stu-id="0373b-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="0373b-263">, Adı 10 ' dan daha uzun.</span><span class="sxs-lookup"><span data-stu-id="0373b-263">With the name longer than 10.</span></span>
* <span data-ttu-id="0373b-264">"Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="0373b-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="0373b-265">döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0373b-265">is returned.</span></span>

<span data-ttu-id="0373b-266">Aşağıdaki modeli göz önünde bulundurun `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0373b-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="0373b-267">Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="0373b-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="0373b-268">`Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="0373b-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="0373b-269">`RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="0373b-270">Yukarıdaki kodda, "tarz":</span><span class="sxs-lookup"><span data-stu-id="0373b-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="0373b-271">Yalnızca harfler kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-271">Must only use letters.</span></span>
  * <span data-ttu-id="0373b-272">İlk harfin büyük harfle olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0373b-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="0373b-273">Boşluk, sayı ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="0373b-274">`RegularExpression`"Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="0373b-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="0373b-275">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0373b-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="0373b-276">Sonraki boşlukların içindeki özel karakter ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="0373b-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="0373b-277">"PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0373b-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="0373b-278">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="0373b-279">`StringLength`Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="0373b-280">Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="0373b-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="0373b-281">Model için Oluştur sayfasında, `Movie` geçersiz değerlere sahip hatalar görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0373b-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="0373b-283">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0373b-283">For more information, see:</span></span>

* [<span data-ttu-id="0373b-284">Film uygulamasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="0373b-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="0373b-285">[ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0373b-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="0373b-286">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="0373b-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="0373b-287">`HEAD`istekler belirli bir kaynak için üstbilgileri almaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="0373b-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="0373b-288">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="0373b-289">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="0373b-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor<span data-ttu-id="0373b-290">Bir `OnGet` işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="0373b-290"> Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="0373b-291">XSRF/CSRF ve Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="0373b-291">XSRF/CSRF and Razor Pages</span></span>

Razor<span data-ttu-id="0373b-292">Sayfalar, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur.</span><span class="sxs-lookup"><span data-stu-id="0373b-292"> Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="0373b-293">[Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="0373b-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="0373b-294">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="0373b-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="0373b-295">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="0373b-296">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*ve *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="0373b-297">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="0373b-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="0373b-298">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="0373b-299">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="0373b-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="0373b-300">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="0373b-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="0373b-301">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="0373b-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="0373b-302">RazorSayfanın içeriği `@RenderBody()` olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="0373b-303">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="0373b-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="0373b-304">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="0373b-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0373b-305">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="0373b-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="0373b-306">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="0373b-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="0373b-307">*Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="0373b-308">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="0373b-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="0373b-309">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="0373b-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="0373b-310">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="0373b-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="0373b-311">Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="0373b-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="0373b-312">Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="0373b-313">MVC denetleyicileri ve geleneksel görünümler ile kullanılan düzenler, şablonlar ve partilar Razor *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="0373b-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="0373b-314">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0373b-315">`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="0373b-316">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="0373b-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="0373b-317">`@namespace`Bir sayfada ayarlanan yönerge:</span><span class="sxs-lookup"><span data-stu-id="0373b-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="0373b-318">`@namespace`Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="0373b-319">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="0373b-320">`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="0373b-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="0373b-321">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="0373b-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="0373b-322">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="0373b-323">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="0373b-324">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="0373b-325">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*</span><span class="sxs-lookup"><span data-stu-id="0373b-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="0373b-326">*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="0373b-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="0373b-327">Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="0373b-328">Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="0373b-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="0373b-329">Düzen dosyası JavaScript dosyalarını içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="0373b-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="0373b-330">[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="0373b-331">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="0373b-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="0373b-332">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="0373b-332">URL generation for Pages</span></span>

<span data-ttu-id="0373b-333">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="0373b-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="0373b-334">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0373b-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="0373b-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="0373b-335">*/Pages*</span></span>

  * <span data-ttu-id="0373b-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="0373b-337">*Gizlilik. cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="0373b-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="0373b-338">*/Customers*</span></span>

    * <span data-ttu-id="0373b-339">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="0373b-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="0373b-340">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="0373b-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-341">*Index.cshtml*</span></span>

<span data-ttu-id="0373b-342">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="0373b-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="0373b-343">Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="0373b-344">*Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="0373b-345">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0373b-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="0373b-346">Mutlak sayfa adı, `/Index` *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="0373b-347">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0373b-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="0373b-348">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="0373b-349">Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="0373b-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="0373b-350">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="0373b-351">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="0373b-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="0373b-352">Aşağıdaki tabloda, `RedirectToPage` *sayfalarda/müşteriler/Create. cshtml*'de farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0373b-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="0373b-353">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="0373b-353">RedirectToPage(x)</span></span>| <span data-ttu-id="0373b-354">Sayfa</span><span class="sxs-lookup"><span data-stu-id="0373b-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0373b-355">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="0373b-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="0373b-356">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-356">*Pages/Index*</span></span> |
| <span data-ttu-id="0373b-357">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="0373b-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="0373b-358">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="0373b-359">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="0373b-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="0373b-360">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-360">*Pages/Index*</span></span> |
| <span data-ttu-id="0373b-361">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="0373b-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="0373b-362">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="0373b-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="0373b-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="0373b-364">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="0373b-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="0373b-365">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="0373b-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="0373b-366">Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="0373b-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="0373b-367">Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.</span><span class="sxs-lookup"><span data-stu-id="0373b-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="0373b-368">Klasör adını içermediği için bağlantılar kopuk değildir.</span><span class="sxs-lookup"><span data-stu-id="0373b-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="0373b-369">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="0373b-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="0373b-370">Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="0373b-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="0373b-371">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="0373b-371">ViewData attribute</span></span>

<span data-ttu-id="0373b-372">Veri, ile bir sayfaya geçirilebilir <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="0373b-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="0373b-373">Özniteliği olan Özellikler `[ViewData]` , değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="0373b-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="0373b-374">Aşağıdaki örnekte, `AboutModel` `[ViewData]` özniteliği `Title` özelliğine uygular:</span><span class="sxs-lookup"><span data-stu-id="0373b-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="0373b-375">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="0373b-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="0373b-376">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="0373b-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="0373b-377">TempData</span><span class="sxs-lookup"><span data-stu-id="0373b-377">TempData</span></span>

<span data-ttu-id="0373b-378">ASP.NET Core, öğesini kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="0373b-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="0373b-379">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="0373b-379">This property stores data until it's read.</span></span> <span data-ttu-id="0373b-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="0373b-381">`TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="0373b-382">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="0373b-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="0373b-383">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="0373b-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="0373b-384">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="0373b-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="0373b-385">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="0373b-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="0373b-386">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="0373b-386">Multiple handlers per page</span></span>

<span data-ttu-id="0373b-387">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="0373b-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="0373b-388">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0373b-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="0373b-389">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="0373b-390">`asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0373b-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="0373b-391">`asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="0373b-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="0373b-392">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="0373b-393">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="0373b-394">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="0373b-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="0373b-395">Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="0373b-396">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="0373b-397">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="0373b-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="0373b-398">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="0373b-399">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="0373b-399">Custom routes</span></span>

<span data-ttu-id="0373b-400">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0373b-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="0373b-401">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="0373b-401">Specify a custom route to a page.</span></span> <span data-ttu-id="0373b-402">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="0373b-403">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-403">Append segments to a page's default route.</span></span> <span data-ttu-id="0373b-404">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="0373b-405">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="0373b-406">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="0373b-407">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="0373b-408">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="0373b-409">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0373b-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="0373b-410">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="0373b-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="0373b-411">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="0373b-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="0373b-412">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="0373b-413">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0373b-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="0373b-414">Gelişmiş yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="0373b-414">Advanced configuration and settings</span></span>

<span data-ttu-id="0373b-415">Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0373b-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="0373b-416">Gelişmiş seçenekleri yapılandırmak için genişletme yöntemini kullanın <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0373b-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="0373b-417">Sayfalar için <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="0373b-418">Kurallar hakkında daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0373b-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="0373b-419">Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="0373b-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="0373b-420">RazorSayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="0373b-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="0373b-421">Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="0373b-422"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> Razor Sayfalarınızın, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) () olduğunu belirtmek için ekleyin <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> :</span><span class="sxs-lookup"><span data-stu-id="0373b-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="0373b-423">RazorSayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="0373b-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="0373b-424"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>Bu Razor sayfaların, uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="0373b-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="0373b-425">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0373b-425">Additional resources</span></span>

* <span data-ttu-id="0373b-426">Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0373b-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="0373b-427">[Özniteliği ve Razor sayfaları yetkilendir](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="0373b-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* [<span data-ttu-id="0373b-428">Örnek kodu indirme veya görüntüleme</span><span class="sxs-lookup"><span data-stu-id="0373b-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0373b-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="0373b-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="0373b-430">Sayfalar, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir yönüdür.</span><span class="sxs-lookup"><span data-stu-id="0373b-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="0373b-431">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="0373b-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="0373b-432">Bu belge, sayfalara giriş sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="0373b-433">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="0373b-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="0373b-434">Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0373b-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="0373b-435">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="0373b-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0373b-436">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0373b-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0373b-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0373b-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0373b-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0373b-439">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="0373b-440">RazorSayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0373b-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0373b-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0373b-442">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0373b-443">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0373b-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0373b-444">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0373b-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="0373b-445">Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.</span><span class="sxs-lookup"><span data-stu-id="0373b-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0373b-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0373b-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0373b-447">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0373b-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="0373b-448">Sayfaları</span><span class="sxs-lookup"><span data-stu-id="0373b-448"> Pages</span></span>

Razor<span data-ttu-id="0373b-449">*Startup.cs*'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="0373b-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="0373b-450">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="0373b-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="0373b-451">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="0373b-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="0373b-452">Bu, farklı kılan `@page` yönergedir.</span><span class="sxs-lookup"><span data-stu-id="0373b-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="0373b-453">`@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0373b-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="0373b-454">`@page`sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0373b-455">`@page`diğer yapıların davranışını etkiler Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="0373b-456">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0373b-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="0373b-457">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="0373b-458">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="0373b-459">Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0373b-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="0373b-460">Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="0373b-461">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="0373b-462">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="0373b-463">Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0373b-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="0373b-464">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="0373b-464">File name and path</span></span>               | <span data-ttu-id="0373b-465">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="0373b-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0373b-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="0373b-467">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="0373b-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="0373b-468">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="0373b-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="0373b-469">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="0373b-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="0373b-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="0373b-471">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="0373b-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="0373b-472">Notlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-472">Notes:</span></span>

* <span data-ttu-id="0373b-473">Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.</span><span class="sxs-lookup"><span data-stu-id="0373b-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="0373b-474">`Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="0373b-475">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="0373b-475">Write a basic form</span></span>

Razor<span data-ttu-id="0373b-476">Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="0373b-477">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="0373b-478">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0373b-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="0373b-479">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="0373b-480">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="0373b-481">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="0373b-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="0373b-482">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="0373b-483">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="0373b-484">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="0373b-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="0373b-485">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="0373b-486">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="0373b-487">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-487">This separation allows:</span></span>

* <span data-ttu-id="0373b-488">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="0373b-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="0373b-489">Sayfaların [birim testi](xref:test/razor-pages-tests) .</span><span class="sxs-lookup"><span data-stu-id="0373b-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="0373b-490">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="0373b-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="0373b-491">Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0373b-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="0373b-492">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0373b-492">The most common handlers are:</span></span>

* <span data-ttu-id="0373b-493">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0373b-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="0373b-494">[OnGet](#OnGet) örneği.</span><span class="sxs-lookup"><span data-stu-id="0373b-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="0373b-495">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="0373b-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="0373b-496">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="0373b-497">Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="0373b-498">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="0373b-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="0373b-499">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="0373b-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="0373b-500">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="0373b-501">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="0373b-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="0373b-502">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0373b-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="0373b-503">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="0373b-503">Check for validation errors.</span></span>

* <span data-ttu-id="0373b-504">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="0373b-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="0373b-505">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="0373b-506">İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="0373b-507">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="0373b-508">Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için yardımcı yöntemini çağırır `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="0373b-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="0373b-509">`RedirectToPage`, `RedirectToAction` veya ' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur `RedirectToRoute` .</span><span class="sxs-lookup"><span data-stu-id="0373b-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="0373b-510">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="0373b-511">`RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="0373b-512">Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen), `OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="0373b-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="0373b-513">`Page`, bir `PageResult` örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="0373b-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="0373b-514">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="0373b-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="0373b-515">`PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="0373b-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="0373b-516">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="0373b-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="0373b-517">`Customer`Özelliği `[BindProperty]` model bağlamayı kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor<span data-ttu-id="0373b-518">Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="0373b-518"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="0373b-519">Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="0373b-520">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="0373b-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0373b-521">Giriş sayfası (*Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="0373b-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="0373b-522">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0373b-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="0373b-523">*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="0373b-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="0373b-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="0373b-525">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="0373b-526">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="0373b-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="0373b-527">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="0373b-528">Etiket Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="0373b-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="0373b-529">*Pages/Edit. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="0373b-530">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="0373b-531">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="0373b-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="0373b-532">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="0373b-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="0373b-533">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="0373b-534">*Pages/Edit. cshtml. cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="0373b-535">*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="0373b-536">Sil düğmesi HTML biçiminde işlendiğinde, `formaction` için parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="0373b-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="0373b-537">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="0373b-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="0373b-538">`handler`Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="0373b-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="0373b-539">Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1` :</span><span class="sxs-lookup"><span data-stu-id="0373b-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="0373b-540">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="0373b-541">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="0373b-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="0373b-542">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="0373b-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="0373b-543">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="0373b-544">Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="0373b-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="0373b-545">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0373b-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="0373b-546">Sorgu dizesinden öğesini kabul eder `id` .</span><span class="sxs-lookup"><span data-stu-id="0373b-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="0373b-547">*Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması içeriyorsa `"{id:int?}"` , `id` Rota verilerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="0373b-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="0373b-548">İçin rota verileri, gibi `id` URI 'de belirtilmiştir `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="0373b-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="0373b-549">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="0373b-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="0373b-550">Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0373b-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="0373b-551">Veritabanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="0373b-551">The database is updated.</span></span>
* <span data-ttu-id="0373b-552">`RedirectToPage`Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="0373b-553">Sayfa özelliklerini gerektiği gibi işaretle</span><span class="sxs-lookup"><span data-stu-id="0373b-553">Mark page properties as required</span></span>

<span data-ttu-id="0373b-554">İçindeki Özellikler `PageModel` [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:</span><span class="sxs-lookup"><span data-stu-id="0373b-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="0373b-555">Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0373b-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="0373b-556">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="0373b-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="0373b-557">`HEAD`istekleri belirli bir kaynak için üstbilgileri almanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="0373b-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="0373b-558">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="0373b-559">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="0373b-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="0373b-560">ASP.NET Core 2,1 veya üzeri sürümlerde, Razor `OnGet` hiçbir işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="0373b-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="0373b-561">Bu davranış, içindeki [Setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0373b-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="0373b-562">Varsayılan Şablonlar `SetCompatibilityVersion` ASP.NET Core 2,1 ve 2,2 ' de çağrıyı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0373b-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="0373b-563">`SetCompatibilityVersion`Razorsayfa seçeneğini etkin bir `AllowMappingHeadRequestsToGetHandler` şekilde ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="0373b-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="0373b-564">İle tüm davranışlardan çıkmak yerine `SetCompatibilityVersion` , açıkça *belirli* davranışları kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0373b-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="0373b-565">Aşağıdaki kod, isteklerin işleyiciye eşlenmesine izin vermek için ' de `HEAD` kullanılır `OnGet` :</span><span class="sxs-lookup"><span data-stu-id="0373b-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="0373b-566">XSRF/CSRF ve Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="0373b-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="0373b-567">[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="0373b-568">Antiforgery belirteci oluşturma ve doğrulama, sayfalara otomatik olarak eklenir Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="0373b-569">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="0373b-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="0373b-570">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="0373b-571">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*, *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="0373b-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="0373b-572">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="0373b-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="0373b-573">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="0373b-574">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="0373b-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="0373b-575">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="0373b-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="0373b-576">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="0373b-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="0373b-577">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="0373b-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="0373b-578">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="0373b-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0373b-579">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="0373b-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="0373b-580">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="0373b-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="0373b-581">*Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="0373b-582">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="0373b-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="0373b-583">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="0373b-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="0373b-584">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="0373b-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="0373b-585">Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="0373b-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="0373b-586">Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="0373b-587">MVC denetleyicileri ve geleneksel görünümler ile kullanmakta olduğunuz düzenler, şablonlar ve parals Razor *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="0373b-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="0373b-588">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0373b-589">`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0373b-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="0373b-590">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="0373b-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="0373b-591">`@namespace`Yönerge bir sayfada açıkça kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="0373b-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="0373b-592">Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0373b-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="0373b-593">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0373b-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="0373b-594">`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="0373b-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="0373b-595">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="0373b-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="0373b-596">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="0373b-597">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0373b-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="0373b-598">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="0373b-599">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*</span><span class="sxs-lookup"><span data-stu-id="0373b-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="0373b-600">Özgün *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="0373b-601">Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="0373b-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="0373b-602">[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="0373b-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="0373b-603">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="0373b-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="0373b-604">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="0373b-604">URL generation for Pages</span></span>

<span data-ttu-id="0373b-605">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="0373b-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="0373b-606">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0373b-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="0373b-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="0373b-607">*/Pages*</span></span>

  * <span data-ttu-id="0373b-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="0373b-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="0373b-609">*/Customers*</span></span>

    * <span data-ttu-id="0373b-610">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="0373b-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="0373b-611">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="0373b-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0373b-612">*Index.cshtml*</span></span>

<span data-ttu-id="0373b-613">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="0373b-614">Dize, `/Index` önceki sayfaya erişmek IÇIN URI 'nin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="0373b-615">Dize, `/Index` *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="0373b-616">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0373b-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="0373b-617">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="0373b-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="0373b-618">Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="0373b-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="0373b-619">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="0373b-620">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="0373b-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="0373b-621">Aşağıdaki tabloda, `RedirectToPage` *sayfa/müşteri/oluşturma. cshtml*'den farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0373b-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="0373b-622">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="0373b-622">RedirectToPage(x)</span></span>| <span data-ttu-id="0373b-623">Sayfa</span><span class="sxs-lookup"><span data-stu-id="0373b-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0373b-624">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="0373b-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="0373b-625">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-625">*Pages/Index*</span></span> |
| <span data-ttu-id="0373b-626">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="0373b-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="0373b-627">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="0373b-628">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="0373b-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="0373b-629">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-629">*Pages/Index*</span></span> |
| <span data-ttu-id="0373b-630">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="0373b-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="0373b-631">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="0373b-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="0373b-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="0373b-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="0373b-633">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="0373b-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="0373b-634">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="0373b-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="0373b-635">Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0373b-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="0373b-636">Tüm bağlantılar hala çalışır (klasör adını içermediği için).</span><span class="sxs-lookup"><span data-stu-id="0373b-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="0373b-637">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="0373b-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="0373b-638">Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="0373b-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="0373b-639">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="0373b-639">ViewData attribute</span></span>

<span data-ttu-id="0373b-640">Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="0373b-641">' Deki denetleyicilerde veya Razor sayfa modellerinde bulunan özelliklerin `[ViewData]` değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="0373b-642">Aşağıdaki örnekte, `AboutModel` `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="0373b-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="0373b-643">`Title`Özelliği hakkında sayfasının başlığına ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="0373b-643">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="0373b-644">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="0373b-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="0373b-645">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="0373b-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="0373b-646">TempData</span><span class="sxs-lookup"><span data-stu-id="0373b-646">TempData</span></span>

<span data-ttu-id="0373b-647">ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="0373b-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="0373b-648">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="0373b-648">This property stores data until it's read.</span></span> <span data-ttu-id="0373b-649">`Keep`Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0373b-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="0373b-650">`TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0373b-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="0373b-651">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="0373b-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="0373b-652">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="0373b-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="0373b-653">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="0373b-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="0373b-654">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="0373b-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="0373b-655">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="0373b-655">Multiple handlers per page</span></span>

<span data-ttu-id="0373b-656">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="0373b-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="0373b-657">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0373b-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="0373b-658">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="0373b-659">`asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0373b-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="0373b-660">`asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="0373b-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="0373b-661">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="0373b-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="0373b-662">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="0373b-663">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="0373b-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="0373b-664">Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="0373b-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="0373b-665">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="0373b-666">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="0373b-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="0373b-667">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="0373b-668">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="0373b-668">Custom routes</span></span>

<span data-ttu-id="0373b-669">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0373b-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="0373b-670">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="0373b-670">Specify a custom route to a page.</span></span> <span data-ttu-id="0373b-671">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="0373b-672">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-672">Append segments to a page's default route.</span></span> <span data-ttu-id="0373b-673">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="0373b-674">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0373b-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="0373b-675">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="0373b-676">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="0373b-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="0373b-677">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="0373b-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="0373b-678">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0373b-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="0373b-679">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="0373b-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="0373b-680">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="0373b-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="0373b-681">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="0373b-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="0373b-682">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0373b-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="0373b-683">Yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="0373b-683">Configuration and settings</span></span>

<span data-ttu-id="0373b-684">Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC Oluşturucu 'da genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0373b-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="0373b-685">Şu anda `RazorPagesOptions` ' nı, sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0373b-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="0373b-686">Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.</span><span class="sxs-lookup"><span data-stu-id="0373b-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="0373b-687">Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="0373b-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="0373b-688">[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="0373b-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="0373b-689">Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0373b-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="0373b-690">RazorSayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="0373b-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="0373b-691">Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="0373b-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="0373b-692">Sayfalarınızın [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) , uygulamanın [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) Razor [içerik kökünde](xref:fundamentals/index#content-root) ([Contentrootpath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) olduğunu belirtmek için addmvc ' ye WithRazorPagesAtContentRoot ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0373b-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="0373b-693">RazorSayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="0373b-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="0373b-694">Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin Razor (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="0373b-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="0373b-695">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0373b-695">Additional resources</span></span>

* <span data-ttu-id="0373b-696">[Özniteliği ve Razor sayfaları yetkilendir](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="0373b-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
