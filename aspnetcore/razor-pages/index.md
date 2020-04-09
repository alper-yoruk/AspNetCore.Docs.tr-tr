---
title: ASP.NET Core'da Jilet Sayfalarına Giriş
author: Rick-Anderson
description: ASP.NET Core'daki Razor Pages'in sayfa odaklı senaryoları kodlamayı MVC kullanmaktan nasıl daha kolay ve üretken hale getirir hale ettiğini öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667582"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="56b9e-103">ASP.NET Core'da Jilet Sayfalarına Giriş</span><span class="sxs-lookup"><span data-stu-id="56b9e-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56b9e-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="56b9e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="56b9e-105">Razor Pages, sayfa odaklı senaryoları kodlamayı denetleyicileri ve görünümleri kullanmaktan daha kolay ve üretken hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="56b9e-106">Model-Görünüm-Denetleyici yaklaşımını kullanan bir öğretici arıyorsanız, ASP.NET [bkz.](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="56b9e-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="56b9e-107">Bu belge, Razor Pages için bir giriş sağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="56b9e-108">Bu adım öğretici bir adım değil.</span><span class="sxs-lookup"><span data-stu-id="56b9e-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="56b9e-109">Bazı bölümleri çok gelişmiş bulursanız, [bkz.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="56b9e-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="56b9e-110">ASP.NET Core'a genel bakış için [ASP.NET Çekirdeğine Giriş bölümüne](xref:index)bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56b9e-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="56b9e-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56b9e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="56b9e-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56b9e-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56b9e-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="56b9e-115">Jilet Sayfaları projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="56b9e-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56b9e-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56b9e-117">Razor Pages projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="56b9e-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56b9e-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56b9e-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56b9e-119">Komut `dotnet new webapp` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56b9e-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56b9e-121">Komut `dotnet new webapp` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="56b9e-122">Mac için Visual Studio'dan oluşturulan *.csproj* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="56b9e-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="56b9e-123">Razor Pages</span></span>

<span data-ttu-id="56b9e-124">Jilet Sayfaları *Startup.cs*olarak etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="56b9e-125">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="56b9e-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="56b9e-126">Önceki kod denetleyicileri ve görünümleri ile ASP.NET Core uygulamasında kullanılan bir [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) na çok benzer.</span><span class="sxs-lookup"><span data-stu-id="56b9e-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="56b9e-127">Onu farklı kılan [`@page`](xref:mvc/views/razor#page) direktiftir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="56b9e-128">`@page`dosyayı bir MVC eylemine dönüştürür - bu da bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="56b9e-129">`@page`bir sayfadaki ilk Razor direktifi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="56b9e-130">`@page`diğer [Razor](xref:mvc/views/razor) yapılarının davranışını etkiler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="56b9e-131">Razor Pages dosya adlarında *.cshtml* soneki vardır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="56b9e-132">Benzer bir sayfa, `PageModel` bir sınıf kullanarak, aşağıdaki iki dosya gösterilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="56b9e-133">*Sayfalar/Index2.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="56b9e-134">*Sayfalar/Index2.cshtml.cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="56b9e-135">Sözleşmeye `PageModel` göre, sınıf dosyası *.cs* eklenen Razor Page dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="56b9e-136">Örneğin, önceki Razor Page *Pages/Index2.cshtml*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="56b9e-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="56b9e-137">`PageModel` Sınıfı içeren dosya *Sayfalar/Index2.cshtml.cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="56b9e-138">URL yollarının sayfalara çağrışımları, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="56b9e-139">Aşağıdaki tabloda Bir Jilet Sayfası yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="56b9e-140">Dosya adı ve yol</span><span class="sxs-lookup"><span data-stu-id="56b9e-140">File name and path</span></span>               | <span data-ttu-id="56b9e-141">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="56b9e-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="56b9e-142">*/Sayfalar/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="56b9e-143">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="56b9e-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="56b9e-144">*/Sayfalar/İletişim.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="56b9e-145">*/Sayfalar/Mağaza/İletişim.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="56b9e-146">*/Sayfalar/Mağaza/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="56b9e-147">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="56b9e-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="56b9e-148">Notlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-148">Notes:</span></span>

* <span data-ttu-id="56b9e-149">Çalışma zamanı varsayılan olarak *Sayfalar* klasöründe Razor Pages dosyalarını arar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="56b9e-150">`Index`bir URL sayfa içermediği varsayılan sayfadır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="56b9e-151">Temel bir form yazma</span><span class="sxs-lookup"><span data-stu-id="56b9e-151">Write a basic form</span></span>

<span data-ttu-id="56b9e-152">Razor Pages, bir uygulama kurarken web tarayıcılarıyla kullanılan yaygın desenleri kolayca uygulamak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="56b9e-153">[Model bağlama,](xref:mvc/models/model-binding) [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML yardımcıları, tüm bunlar jilet sayfası sınıfında tanımlanan özelliklerle *çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="56b9e-154">`Contact` Model için temel bir "bize ulaşın" formu uygulayan bir sayfa düşünün:</span><span class="sxs-lookup"><span data-stu-id="56b9e-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="56b9e-155">Bu belgedeki örnekler `DbContext` [için, Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başharf.</span><span class="sxs-lookup"><span data-stu-id="56b9e-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="56b9e-156">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="56b9e-157">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="56b9e-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="56b9e-158">*Pages/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="56b9e-159">*Sayfalar/Create.cshtml.cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="56b9e-160">Kural olarak, `PageModel` sınıf `<PageName>Model` çağrılır ve sayfayla aynı ad alanındadır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="56b9e-161">Sınıf, `PageModel` bir sayfanın mantığının sunusundan ayrılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="56b9e-162">Sayfaya gönderilen istekler ve sayfayı işlemek için kullanılan veriler için sayfa işleyicilerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="56b9e-163">Bu ayırma sağlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-163">This separation allows:</span></span>

* <span data-ttu-id="56b9e-164">[Bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarının yönetilmesi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="56b9e-165">Birim testi</span><span class="sxs-lookup"><span data-stu-id="56b9e-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="56b9e-166">Sayfada istekler `OnPostAsync` üzerinde `POST` çalışan bir *işleyici yöntemi*vardır (kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="56b9e-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="56b9e-167">Herhangi bir HTTP fiili için işleyici yöntemleri eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="56b9e-168">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-168">The most common handlers are:</span></span>

* <span data-ttu-id="56b9e-169">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="56b9e-170">Önceki kodda, `OnGet` yöntem *CreateModel.cshtml* Razor Sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="56b9e-171">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="56b9e-172">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="56b9e-173">Önceki kod, Razor Pages için tipiktir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="56b9e-174">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamalara aşinaysanız:</span><span class="sxs-lookup"><span data-stu-id="56b9e-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="56b9e-175">Önceki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer görünüyor.</span><span class="sxs-lookup"><span data-stu-id="56b9e-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="56b9e-176">[Model bağlama,](xref:mvc/models/model-binding) [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi MVC ilkel çoğu Denetleyicileri ve Jilet Sayfaları ile aynı çalışır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="56b9e-177">Önceki `OnPostAsync` yöntem:</span><span class="sxs-lookup"><span data-stu-id="56b9e-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="56b9e-178">Temel `OnPostAsync`akış:</span><span class="sxs-lookup"><span data-stu-id="56b9e-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="56b9e-179">Doğrulama hatalarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-179">Check for validation errors.</span></span>

* <span data-ttu-id="56b9e-180">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="56b9e-181">Hatalar varsa, sayfayı doğrulama iletileriyle yeniden gösterin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="56b9e-182">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="56b9e-183">*Pages/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="56b9e-184">*Pages/Create.cshtml*adresinden işlenen HTML :</span><span class="sxs-lookup"><span data-stu-id="56b9e-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="56b9e-185">Önceki kodda, formu deftere nakletmek:</span><span class="sxs-lookup"><span data-stu-id="56b9e-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="56b9e-186">Geçerli verilerle:</span><span class="sxs-lookup"><span data-stu-id="56b9e-186">With valid data:</span></span>

  * <span data-ttu-id="56b9e-187">Işleyici `OnPostAsync` yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="56b9e-188">`RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="56b9e-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="56b9e-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="56b9e-190">Bir eylem sonucudur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-190">Is an action result.</span></span>
    * <span data-ttu-id="56b9e-191">Benzer `RedirectToAction` veya `RedirectToRoute` (denetleyicileri ve görünümleri kullanılır).</span><span class="sxs-lookup"><span data-stu-id="56b9e-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="56b9e-192">Sayfalar için özelleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-192">Is customized for pages.</span></span> <span data-ttu-id="56b9e-193">Önceki örnekte, kök Dizin sayfasına yönlendirir`/Index`( ).</span><span class="sxs-lookup"><span data-stu-id="56b9e-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="56b9e-194">`RedirectToPage`Sayfalar için [URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="56b9e-195">Sunucuya geçirilen doğrulama hatalarıyla:</span><span class="sxs-lookup"><span data-stu-id="56b9e-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="56b9e-196">Işleyici `OnPostAsync` yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="56b9e-197">`Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="56b9e-198">İade, `Page` denetleyicideki eylemlerin nasıl geri `View`döndüğüne benzer.</span><span class="sxs-lookup"><span data-stu-id="56b9e-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="56b9e-199">`PageResult`işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="56b9e-200">Sayfayı döndüren `void` bir işleyici yöntemi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="56b9e-201">Önceki örnekte, [modelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış dönen hiçbir değer sonuçları ile form gönderme.</span><span class="sxs-lookup"><span data-stu-id="56b9e-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="56b9e-202">Bu örnekte, istemcide doğrulama hatası görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="56b9e-203">Doğrulama hatası teslimi daha sonra bu belgede ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="56b9e-204">İstemci tarafı doğrulama tarafından algılanan doğrulama hataları ile:</span><span class="sxs-lookup"><span data-stu-id="56b9e-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="56b9e-205">Veriler sunucuya **nakledilmez.**</span><span class="sxs-lookup"><span data-stu-id="56b9e-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="56b9e-206">İstemci tarafı doğrulaması daha sonra bu belgede açıklanır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="56b9e-207">Özellik, `Customer` [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) bağlamayı modellemeyi tercih etmek için öznitelik kullanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="56b9e-208">`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikleri içeren modellerde **kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="56b9e-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="56b9e-209">Daha fazla bilgi için [bkz.](xref:data/ef-rp/crud#overposting)</span><span class="sxs-lookup"><span data-stu-id="56b9e-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="56b9e-210">Razor Pages, varsayılan olarak, yalnızca`GET` olmayan fiillerle özellikleri bağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="56b9e-211">Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma gereksinimini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="56b9e-212">Bağlama, form alanlarını işlemek için aynı`<input asp-for="Customer.Name">`özelliği kullanarak kodu azaltır ( ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="56b9e-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="56b9e-213">*Pages/Create.cshtml* görünüm dosyasını gözden geçirme:</span><span class="sxs-lookup"><span data-stu-id="56b9e-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="56b9e-214">Önceki kodda, [giriş etiketi yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini model ifadesine `Customer.Name` bağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="56b9e-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Tag Yardımcıları kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="56b9e-216">Ana sayfa</span><span class="sxs-lookup"><span data-stu-id="56b9e-216">The home page</span></span>

<span data-ttu-id="56b9e-217">*Index.cshtml* ana sayfadır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="56b9e-218">İlişkili `PageModel` sınıf (*Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="56b9e-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="56b9e-219">*Index.cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="56b9e-220">`<a /a>` [Çapa Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-route-{value}` Edit sayfasına bir bağlantı oluşturmak için özniteliği kullandı.</span><span class="sxs-lookup"><span data-stu-id="56b9e-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="56b9e-221">Bağlantı, ilgili kişi kimliğiyle birlikte rota verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="56b9e-222">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="56b9e-223">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="56b9e-224">*Index.cshtml* dosyası, her müşteri ilgili kişi için bir silme düğmesi oluşturmak için işaretleme içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="56b9e-225">İşlenen HTML:</span><span class="sxs-lookup"><span data-stu-id="56b9e-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="56b9e-226">Silme düğmesi HTML'de işlendiğinde, [biçimeylemi](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) aşağıdakiparametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="56b9e-227">Öznitelik tarafından `asp-route-id` belirtilen müşteri iletişim kimliği.</span><span class="sxs-lookup"><span data-stu-id="56b9e-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="56b9e-228">Öznitelik `handler`tarafından `asp-page-handler` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="56b9e-229">Düğme seçildiğinde, sunucuya `POST` bir form isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="56b9e-230">Kural kuralına göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="56b9e-231">Bu `handler` örnekte olduğundan, `delete` `OnPostDeleteAsync` `POST` işleyici yöntemi isteği işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="56b9e-232">Farklı `asp-page-handler` bir değere ayarlanmışsa, `remove`örneğin , adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="56b9e-233">Yöntem: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="56b9e-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="56b9e-234">Sorgu `id` dizesinden alır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="56b9e-235">Müşteri ile ilgili iletişim için `FindAsync`veritabanını sorgular.</span><span class="sxs-lookup"><span data-stu-id="56b9e-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="56b9e-236">Müşteri ilgili kişi bulunursa, kaldırılır ve veritabanı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="56b9e-237">Kök <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> Dizin sayfasına yönlendirmek`/Index`için çağrılar ( ).</span><span class="sxs-lookup"><span data-stu-id="56b9e-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="56b9e-238">Edit.cshtml dosyası</span><span class="sxs-lookup"><span data-stu-id="56b9e-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="56b9e-239">İlk satır yönergeyi `@page "{id:int}"` içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="56b9e-240">Yönlendirme kısıtlaması,`"{id:int}"` sayfaya rota verilerini içeren `int` sayfadaki istekleri kabul etmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="56b9e-241">Sayfaya yapılan bir `int`istek, bir ,çalışma zamanı http 404 (bulunamadı) hatasına dönüştürülebilecek rota verileri içermiyorsa.</span><span class="sxs-lookup"><span data-stu-id="56b9e-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="56b9e-242">Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:</span><span class="sxs-lookup"><span data-stu-id="56b9e-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="56b9e-243">Edit.cshtml.cs *Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="56b9e-244">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="56b9e-244">Validation</span></span>

<span data-ttu-id="56b9e-245">Doğrulama kuralları:</span><span class="sxs-lookup"><span data-stu-id="56b9e-245">Validation rules:</span></span>

* <span data-ttu-id="56b9e-246">Model sınıfında bildirimsel olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="56b9e-247">Uygulamanın her yerinde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="56b9e-248">Ad <xref:System.ComponentModel.DataAnnotations> alanı, bir sınıfa veya özelliğe bildirimsel olarak uygulanan yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="56b9e-249">DataAnnotations da biçimlendirme [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) ile yardımcı gibi biçimlendirme öznitelikleri içerir ve herhangi bir doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="56b9e-250">Modeli `Customer` göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="56b9e-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="56b9e-251">Aşağıdaki *Create.cshtml* görünüm dosyasını kullanma:</span><span class="sxs-lookup"><span data-stu-id="56b9e-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="56b9e-252">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="56b9e-252">The preceding code:</span></span>

* <span data-ttu-id="56b9e-253">jQuery ve jQuery doğrulama komut dosyalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="56b9e-254">Etkinleştirmek `<div />` `<span />` için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="56b9e-255">İstemci tarafı doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="56b9e-255">Client-side validation.</span></span>
  * <span data-ttu-id="56b9e-256">Doğrulama hatası oluşturma.</span><span class="sxs-lookup"><span data-stu-id="56b9e-256">Validation error rendering.</span></span>

* <span data-ttu-id="56b9e-257">Aşağıdaki HTML'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="56b9e-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="56b9e-258">Ad değeri olmadan Form Oluşturma'da hata iletisi görüntülenir"Ad alanı gereklidir."</span><span class="sxs-lookup"><span data-stu-id="56b9e-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="56b9e-259">şeklinde.</span><span class="sxs-lookup"><span data-stu-id="56b9e-259">on the form.</span></span> <span data-ttu-id="56b9e-260">İstemcide JavaScript etkinleştirilmişse, tarayıcı hatayı sunucuya göndermeden görüntüler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="56b9e-261">Öznitelik `[StringLength(10)]` işlenen `data-val-length-max="10"` HTML üzerinde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="56b9e-262">`data-val-length-max`tarayıcıların belirtilen maksimum uzunluktan daha fazla girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="56b9e-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="56b9e-263">Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılırsa:</span><span class="sxs-lookup"><span data-stu-id="56b9e-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="56b9e-264">Adı 10'dan uzun.</span><span class="sxs-lookup"><span data-stu-id="56b9e-264">With the name longer than 10.</span></span>
* <span data-ttu-id="56b9e-265">Hata iletisi "Alan Adı en fazla 10 uzunluğa sahip bir dize olmalıdır."</span><span class="sxs-lookup"><span data-stu-id="56b9e-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="56b9e-266">döndürülür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-266">is returned.</span></span>

<span data-ttu-id="56b9e-267">Aşağıdaki `Movie` modeli göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="56b9e-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="56b9e-268">Doğrulama öznitelikleri, uygulandıkları model özellikleriüzerinde uygulanacak davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="56b9e-269">Ve `Required` `MinimumLength` öznitelikleri bir özelliğin bir değeri olması gerektiğini gösterir, ancak hiçbir şey bu doğrulamayı karşılamak için beyaz boşluk girmesini kullanıcı engeller.</span><span class="sxs-lookup"><span data-stu-id="56b9e-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="56b9e-270">Öznitelik, `RegularExpression` hangi karakterlerin giriş olabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="56b9e-271">Önceki kodda, "Tür":</span><span class="sxs-lookup"><span data-stu-id="56b9e-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="56b9e-272">Sadece harfleri kullanmalısınız.</span><span class="sxs-lookup"><span data-stu-id="56b9e-272">Must only use letters.</span></span>
  * <span data-ttu-id="56b9e-273">İlk harfin büyük harf olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="56b9e-274">Beyaz boşluk, sayılar ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="56b9e-275">`RegularExpression` "Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="56b9e-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="56b9e-276">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="56b9e-277">Sonraki alanlarda özel karakterlere ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="56b9e-278">"PG-13" bir derecelendirme için geçerlidir, ancak bir "Tür" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="56b9e-279">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="56b9e-280">Öznitelik, `StringLength` dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en az uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="56b9e-281">Değer türleri (, `int` `float`, `DateTime`, , ) doğal `[Required]` `decimal`olarak gereklidir ve öznitelik gerekmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="56b9e-282">Modeliçin `Movie` Oluştur sayfası geçersiz değerlere sahip hataları gösterir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası olan film görüntüleme formu](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="56b9e-284">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-284">For more information, see:</span></span>

* [<span data-ttu-id="56b9e-285">Film uygulamasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="56b9e-286">[ASP.NET Core'da model doğrulama.](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="56b9e-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="56b9e-287">OnGet işleyicisi geri dönüşle HEAD isteklerini işleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="56b9e-288">`HEAD`istekleri belirli bir kaynak için üstbilgi alma sağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="56b9e-289">İsteklerden `GET` `HEAD` farklı olarak, istekler yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="56b9e-290">Normalde, bir `OnHead` işleyici oluşturulur ve istekler için `HEAD` çağrılır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="56b9e-291">Razor Pages, işleyici `OnGet` tanımlanmamışsa `OnHead` işleyiciyi aramaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="56b9e-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="56b9e-292">XSRF/CSRF ve Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="56b9e-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="56b9e-293">Jilet Sayfaları [Antiforgery doğrulama](xref:security/anti-request-forgery)ile korunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="56b9e-294">[FormTagHelper,](xref:mvc/views/working-with-forms#the-form-tag-helper) HTML form öğelerine antiforgery belirteçleri enjekte eder.</span><span class="sxs-lookup"><span data-stu-id="56b9e-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="56b9e-295">Düzenleri, kısmileri, şablonları ve Yardımcıları Jilet Li Sayfalarla Etiketleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="56b9e-296">Sayfalar, Razor görünüm motorunun tüm özellikleriyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="56b9e-297">Düzenleri, partials, şablonlar, Tag Helpers, *_ViewStart.cshtml*ve *_ViewImports.cshtml* geleneksel Razor görünümleri için yaptıkları gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="56b9e-298">Bu özelliklerden bazılarını yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="56b9e-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="56b9e-299">*Sayfalara/Paylaşılan/_Layout.cshtml'e* [düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="56b9e-300">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="56b9e-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="56b9e-301">Her sayfanın düzenini denetler (sayfa mizanpajı devre dışı bırakmadığı sürece).</span><span class="sxs-lookup"><span data-stu-id="56b9e-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="56b9e-302">JavaScript ve stylesheets gibi HTML yapılarını içeri aktlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="56b9e-303">Razor sayfasının içeriği, çağrıldığı `@RenderBody()` yerde işlenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="56b9e-304">Daha fazla bilgi için [düzen sayfasına](xref:mvc/views/layout)bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="56b9e-305">[Düzen](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart.cshtml*olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="56b9e-306">Düzen *Sayfalar/Paylaşılanklasöründedir.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="56b9e-307">Sayfalar, geçerli sayfayla aynı klasörden başlayarak hiyerarşik olarak diğer görünümleri (düzenler, şablonlar, kısmiler) arar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="56b9e-308">*Sayfalar/Paylaşılan* klasöründeki bir *düzen, Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="56b9e-309">Düzen dosyası *Sayfalar/Paylaşılan* klasörüne gitmeli.</span><span class="sxs-lookup"><span data-stu-id="56b9e-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="56b9e-310">Düzen dosyasını *Görünümler/Paylaşılanklasörüne* **koymamanızı** öneririz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="56b9e-311">*Görünümler/Paylaşılanlar* bir MVC görünüm desenidir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="56b9e-312">Razor Pages, yol kurallarına değil, klasör hiyerarşisine güvenmek içindir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="56b9e-313">Razor Page'deki aramayı *görüntüleyin, Sayfalar* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="56b9e-314">MVC denetleyicileri ve geleneksel Razor görünümleri ile kullanılan düzenler, şablonlar ve kısmi ler *yalnızca çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="56b9e-315">*Pages/_ViewImports.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="56b9e-316">`@namespace`öğreticidaha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="56b9e-317">Yönerge, `@addTagHelper` yerleşik Tag *Yardımcıları'nı Sayfalar* klasöründeki tüm sayfalara getirir. [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index)</span><span class="sxs-lookup"><span data-stu-id="56b9e-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="56b9e-318">Bir `@namespace` sayfada ayarlanan yönerge:</span><span class="sxs-lookup"><span data-stu-id="56b9e-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="56b9e-319">Yönerge, `@namespace` sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="56b9e-320">Yönerge, `@model` ad alanını içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="56b9e-321">Yönerge *_ViewImports.cshtml'de*yer aldığında, belirtilen ad `@namespace` alanı, yönergeyi içeri yediren Sayfada oluşturulan ad alanı için öneki sağlar. `@namespace`</span><span class="sxs-lookup"><span data-stu-id="56b9e-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="56b9e-322">Oluşturulan ad alanının geri kalanı (sonek bölümü), *_ViewImports.cshtml* içeren klasör ile sayfayı içeren klasör arasındaki nokta-ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="56b9e-323">Örneğin, `PageModel` Sınıf *Pages/Customers/Edit.cshtml.cs* açıkça ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="56b9e-324">*Pages/_ViewImports.cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="56b9e-325">*Sayfalar/Müşteriler/Edit.cshtml* Razor Page için oluşturulan ad alanı `PageModel` sınıfla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="56b9e-326">`@namespace`*ayrıca geleneksel Razor görünümleri ile çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="56b9e-327">*Sayfalar/Create.cshtml* görünüm dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="56b9e-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="56b9e-328">*_ViewImports.cshtml* ve önceki düzen dosyası ile güncelleştirilmiş *Pages/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="56b9e-329">Önceki kodda, *_ViewImports.cshtml* ad alanı ve Tag Helpers'ı aktarDı.</span><span class="sxs-lookup"><span data-stu-id="56b9e-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="56b9e-330">Düzen dosyası JavaScript dosyalarını içe aktardı.</span><span class="sxs-lookup"><span data-stu-id="56b9e-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="56b9e-331">[Razor Pages başlangıç projesi,](#rpvs17) istemci tarafı doğrulaması kancasayfalar/_ValidationScriptsPartial.cshtml içerir. *Pages/_ValidationScriptsPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="56b9e-332">Kısmi görünümler hakkında daha <xref:mvc/views/partial>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="56b9e-333">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="56b9e-333">URL generation for Pages</span></span>

<span data-ttu-id="56b9e-334">Daha `Create` önce gösterilen sayfa `RedirectToPage`yı kullanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="56b9e-335">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="56b9e-336">*/Sayfalar*</span><span class="sxs-lookup"><span data-stu-id="56b9e-336">*/Pages*</span></span>

  * <span data-ttu-id="56b9e-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="56b9e-338">*Gizlilik.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="56b9e-339">*/Müşteriler*</span><span class="sxs-lookup"><span data-stu-id="56b9e-339">*/Customers*</span></span>

    * <span data-ttu-id="56b9e-340">*Oluştur.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="56b9e-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="56b9e-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-342">*Index.cshtml*</span></span>

<span data-ttu-id="56b9e-343">*Sayfalar/Müşteriler/Create.cshtml* ve *Pages/Customers/Edit.cshtml* sayfaları başarıdan sonra *Sayfalar/Müşteriler/Index.cshtml'e* yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="56b9e-344">Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="56b9e-345">*Sayfalar/Müşteriler/Index.cshtml* sayfasına URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="56b9e-346">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="56b9e-347">Mutlak sayfa `/Index` *adı, Pages/Index.cshtml* sayfasına URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="56b9e-348">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="56b9e-349">Sayfa adı, bir satır aralığı `/` (örneğin, `/Index`) dahil olmak üzere kök */Sayfalar* klasöründen sayfaya giden yoldur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="56b9e-350">Önceki URL oluşturma örnekleri, bir URL'yi sıkı kodlamaya göre gelişmiş seçenekler ve işlevsel özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="56b9e-351">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve rotanın hedef yolda nasıl tanımlandığına göre parametreler oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="56b9e-352">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="56b9e-353">Aşağıdaki tabloda, `RedirectToPage` *Sayfalar/Müşteriler/Create.cshtml'de*farklı parametreler kullanılarak hangi Dizin sayfasının seçildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="56b9e-354">YönlendirmePage(x)</span><span class="sxs-lookup"><span data-stu-id="56b9e-354">RedirectToPage(x)</span></span>| <span data-ttu-id="56b9e-355">Sayfa</span><span class="sxs-lookup"><span data-stu-id="56b9e-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="56b9e-356">ReDirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="56b9e-357">*Sayfalar/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-357">*Pages/Index*</span></span> |
| <span data-ttu-id="56b9e-358">ReDirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="56b9e-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="56b9e-359">*Sayfalar/Müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="56b9e-360">YönlendirmePage("... /Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="56b9e-361">*Sayfalar/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-361">*Pages/Index*</span></span> |
| <span data-ttu-id="56b9e-362">ReDirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="56b9e-363">*Sayfalar/Müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="56b9e-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` *göreli adlar*.</span><span class="sxs-lookup"><span data-stu-id="56b9e-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="56b9e-365">Parametre, `RedirectToPage` hedef sayfanın adını hesaplamak için geçerli sayfanın yolu ile *birleştirilir.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="56b9e-366">Göreli ad bağlama, karmaşık bir yapıya sahip siteleri inşa ederken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="56b9e-367">Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="56b9e-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="56b9e-368">Bir klasörü yeniden adlandırmak göreli bağlantıları kesmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="56b9e-369">Klasör adını içermedikleri için bağlantılar kırılmaz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="56b9e-370">Farklı bir [Alan'daki](xref:mvc/controllers/areas)bir sayfaya yönlendirmek için alanı belirtin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="56b9e-371">Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="56b9e-372">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="56b9e-372">ViewData attribute</span></span>

<span data-ttu-id="56b9e-373">Veriler <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>bir sayfaya iletilebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="56b9e-374">Öznitelik `[ViewData]` teki özelliklerin değerleri depolanır ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>'den yüklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="56b9e-375">Aşağıdaki örnekte, `AboutModel` `[ViewData]` `Title` özelliğe öznitelik uygular:</span><span class="sxs-lookup"><span data-stu-id="56b9e-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="56b9e-376">Hakkında sayfasında, `Title` özellik bir model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="56b9e-377">Düzende, başlık ViewData sözlüğünden okunur:</span><span class="sxs-lookup"><span data-stu-id="56b9e-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="56b9e-378">Geçici Veriler</span><span class="sxs-lookup"><span data-stu-id="56b9e-378">TempData</span></span>

<span data-ttu-id="56b9e-379">ASP.NET Core ortaya <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>çıkarır .</span><span class="sxs-lookup"><span data-stu-id="56b9e-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="56b9e-380">Bu özellik, verileri okunana kadar depolar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-380">This property stores data until it's read.</span></span> <span data-ttu-id="56b9e-381">Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silme olmadan verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="56b9e-382">`TempData`tek bir istekten daha fazlası için veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="56b9e-383">Aşağıdaki kod `Message` kullanma `TempData`değerini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="56b9e-384">*Sayfalar/Müşteriler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirme, `Message` kullanmanın `TempData`değerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="56b9e-385">*Sayfalar/Müşteriler/Index.cshtml.cs* sayfa modeli `[TempData]` `Message` özelliğiöznitelik uygular.</span><span class="sxs-lookup"><span data-stu-id="56b9e-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="56b9e-386">Daha fazla bilgi için [TempData'ya](xref:fundamentals/app-state#tempdata)bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="56b9e-387">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="56b9e-387">Multiple handlers per page</span></span>

<span data-ttu-id="56b9e-388">Aşağıdaki sayfa, `asp-page-handler` Etiket Yardımcısı'nı kullanarak iki işleyici için biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="56b9e-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="56b9e-389">Önceki örnekteki formun her biri farklı bir `FormActionTagHelper` URL'ye göndermek için kullanılan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="56b9e-390">Öznitelik `asp-page-handler` bir `asp-page`arkadaşıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="56b9e-391">`asp-page-handler`bir sayfa tarafından tanımlanan işleyici yöntemlerinin her birine gönderen URL'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="56b9e-392">`asp-page`örnek geçerli sayfaya bağlandığı için belirtilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="56b9e-393">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="56b9e-394">Önceki kod *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="56b9e-395">Adlandırılmış işleyici yöntemleri, (varsa) sonra `On<HTTP Verb>` ve `Async` öncesinde ki addaki metin alınarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="56b9e-396">Önceki örnekte, sayfa yöntemleri OnPost**JoinList**Async ve OnPost**JoinListUC**Async'dir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="56b9e-397">*OnPost* ve *Async* kaldırıldı, işleyici `JoinList` `JoinListUC`adları ve .</span><span class="sxs-lookup"><span data-stu-id="56b9e-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="56b9e-398">Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH?handler=JoinList`</span><span class="sxs-lookup"><span data-stu-id="56b9e-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="56b9e-399">Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="56b9e-400">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="56b9e-400">Custom routes</span></span>

<span data-ttu-id="56b9e-401">Yönergeyi `@page` şu şekilde kullanın:</span><span class="sxs-lookup"><span data-stu-id="56b9e-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="56b9e-402">Sayfaya özel bir rota belirtin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-402">Specify a custom route to a page.</span></span> <span data-ttu-id="56b9e-403">Örneğin, Hakkında sayfasına `/Some/Other/Path` giden yol . `@page "/Some/Other/Path"`</span><span class="sxs-lookup"><span data-stu-id="56b9e-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="56b9e-404">Bir sayfanın varsayılan rotasına segmentleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-404">Append segments to a page's default route.</span></span> <span data-ttu-id="56b9e-405">Örneğin, bir "öğe" kesimi sayfanın varsayılan rotasına `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="56b9e-406">Parametreleri sayfanın varsayılan rotasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="56b9e-407">Örneğin, bir kimlik parametresi, `id`, . `@page "{id}"`</span><span class="sxs-lookup"><span data-stu-id="56b9e-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="56b9e-408">Yolun başında ki bir tilde`~`( ) tarafından belirlenen kök-göreli yol desteklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="56b9e-409">Örneğin, `@page "~/Some/Other/Path"` aynı `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="56b9e-410">URL'deki sorgu dizesini `?handler=JoinList` beğenmezseniz, URL'nin yol bölümüne işleyici adını koymak için rotayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="56b9e-411">Rota, `@page` yönergeden sonra çift tırnak içinde bir rota şablonu eklenerek özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="56b9e-412">Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH/JoinList`</span><span class="sxs-lookup"><span data-stu-id="56b9e-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="56b9e-413">Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="56b9e-414">`?` Aşağıdaki, `handler` rota parametresi isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="56b9e-415">Gelişmiş yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="56b9e-415">Advanced configuration and settings</span></span>

<span data-ttu-id="56b9e-416">Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama tarafından gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="56b9e-417">Gelişmiş seçenekleri yapılandırmak için uzantı yöntemini <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>kullanın:</span><span class="sxs-lookup"><span data-stu-id="56b9e-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="56b9e-418">Sayfalar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> için kök dizinini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="56b9e-419">Kongreler hakkında daha fazla bilgi [için, Bkz. Razor Pages yetkilendirme kuralları.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="56b9e-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="56b9e-420">Görünümleri önceden derlemek [için, Bkz. Razor görünüm](xref:mvc/views/view-compilation)derlemesi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="56b9e-421">Razor Sayfalarının içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="56b9e-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="56b9e-422">Varsayılan olarak, Razor Pages */Pages* dizininde köklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="56b9e-423">Razor <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> Sayfalarınızın uygulamanın içerik [kökünde](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>( ) olduğunu belirtmek için ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="56b9e-424">Razor Sayfalarının özel bir kök dizininde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="56b9e-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="56b9e-425">Razor <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> Pages'in uygulamada özel bir kök dizininde olduğunu belirtmek için ekleyin (göreceli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="56b9e-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="56b9e-426">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="56b9e-426">Additional resources</span></span>

* <span data-ttu-id="56b9e-427">Bu giriş üzerine inşa [Razor Pages ile başlayın](xref:tutorials/razor-pages/razor-pages-start)bakın</span><span class="sxs-lookup"><span data-stu-id="56b9e-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="56b9e-428">Örnek kodu indirme veya görüntüleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56b9e-429">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="56b9e-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="56b9e-430">Razor Pages, ASP.NET Core MVC'nin sayfa odaklı senaryoları kodlamayı daha kolay ve üretken hale getiren yeni bir yönüdür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="56b9e-431">Model-Görünüm-Denetleyici yaklaşımını kullanan bir öğretici arıyorsanız, ASP.NET [bkz.](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="56b9e-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="56b9e-432">Bu belge, Razor Pages için bir giriş sağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="56b9e-433">Bu adım öğretici bir adım değil.</span><span class="sxs-lookup"><span data-stu-id="56b9e-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="56b9e-434">Bazı bölümleri çok gelişmiş bulursanız, [bkz.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="56b9e-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="56b9e-435">ASP.NET Core'a genel bakış için [ASP.NET Çekirdeğine Giriş bölümüne](xref:index)bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56b9e-436">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="56b9e-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56b9e-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="56b9e-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56b9e-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56b9e-439">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="56b9e-440">Jilet Sayfaları projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="56b9e-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56b9e-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56b9e-442">Razor Pages projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="56b9e-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56b9e-443">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56b9e-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56b9e-444">Komut `dotnet new webapp` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="56b9e-445">Mac için Visual Studio'dan oluşturulan *.csproj* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56b9e-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56b9e-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56b9e-447">Komut `dotnet new webapp` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="56b9e-448">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="56b9e-448">Razor Pages</span></span>

<span data-ttu-id="56b9e-449">Jilet Sayfaları *Startup.cs*olarak etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="56b9e-450">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="56b9e-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="56b9e-451">Önceki kod denetleyicileri ve görünümleri ile ASP.NET Core uygulamasında kullanılan bir [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) na çok benzer.</span><span class="sxs-lookup"><span data-stu-id="56b9e-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="56b9e-452">Onu farklı kılan `@page` direktiftir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="56b9e-453">`@page`dosyayı bir MVC eylemine dönüştürür - bu da bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="56b9e-454">`@page`bir sayfadaki ilk Razor direktifi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="56b9e-455">`@page`diğer Razor yapılarının davranışını etkiler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="56b9e-456">Benzer bir sayfa, `PageModel` bir sınıf kullanarak, aşağıdaki iki dosya gösterilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="56b9e-457">*Sayfalar/Index2.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="56b9e-458">*Sayfalar/Index2.cshtml.cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="56b9e-459">Sözleşmeye `PageModel` göre, sınıf dosyası *.cs* eklenen Razor Page dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="56b9e-460">Örneğin, önceki Razor Page *Pages/Index2.cshtml*olduğunu.</span><span class="sxs-lookup"><span data-stu-id="56b9e-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="56b9e-461">`PageModel` Sınıfı içeren dosya *Sayfalar/Index2.cshtml.cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="56b9e-462">URL yollarının sayfalara çağrışımları, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="56b9e-463">Aşağıdaki tabloda Bir Jilet Sayfası yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="56b9e-464">Dosya adı ve yol</span><span class="sxs-lookup"><span data-stu-id="56b9e-464">File name and path</span></span>               | <span data-ttu-id="56b9e-465">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="56b9e-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="56b9e-466">*/Sayfalar/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="56b9e-467">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="56b9e-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="56b9e-468">*/Sayfalar/İletişim.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="56b9e-469">*/Sayfalar/Mağaza/İletişim.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="56b9e-470">*/Sayfalar/Mağaza/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="56b9e-471">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="56b9e-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="56b9e-472">Notlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-472">Notes:</span></span>

* <span data-ttu-id="56b9e-473">Çalışma zamanı varsayılan olarak *Sayfalar* klasöründe Razor Pages dosyalarını arar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="56b9e-474">`Index`bir URL sayfa içermediği varsayılan sayfadır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="56b9e-475">Temel bir form yazma</span><span class="sxs-lookup"><span data-stu-id="56b9e-475">Write a basic form</span></span>

<span data-ttu-id="56b9e-476">Razor Pages, bir uygulama kurarken web tarayıcılarıyla kullanılan yaygın desenleri kolayca uygulamak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="56b9e-477">[Model bağlama,](xref:mvc/models/model-binding) [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML yardımcıları, tüm bunlar jilet sayfası sınıfında tanımlanan özelliklerle *çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="56b9e-478">`Contact` Model için temel bir "bize ulaşın" formu uygulayan bir sayfa düşünün:</span><span class="sxs-lookup"><span data-stu-id="56b9e-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="56b9e-479">Bu belgedeki örnekler `DbContext` [için, Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başharf.</span><span class="sxs-lookup"><span data-stu-id="56b9e-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="56b9e-480">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="56b9e-481">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="56b9e-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="56b9e-482">*Pages/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="56b9e-483">*Sayfalar/Create.cshtml.cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="56b9e-484">Kural olarak, `PageModel` sınıf `<PageName>Model` çağrılır ve sayfayla aynı ad alanındadır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="56b9e-485">Sınıf, `PageModel` bir sayfanın mantığının sunusundan ayrılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="56b9e-486">Sayfaya gönderilen istekler ve sayfayı işlemek için kullanılan veriler için sayfa işleyicilerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="56b9e-487">Bu ayırma sağlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-487">This separation allows:</span></span>

* <span data-ttu-id="56b9e-488">[Bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarının yönetilmesi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="56b9e-489">Ünite sayfaları [test ediyor.](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="56b9e-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="56b9e-490">Sayfada istekler `OnPostAsync` üzerinde `POST` çalışan bir *işleyici yöntemi*vardır (kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="56b9e-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="56b9e-491">Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="56b9e-492">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-492">The most common handlers are:</span></span>

* <span data-ttu-id="56b9e-493">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="56b9e-494">[OnGet](#OnGet) örnek.</span><span class="sxs-lookup"><span data-stu-id="56b9e-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="56b9e-495">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="56b9e-496">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="56b9e-497">Önceki kod, Razor Pages için tipiktir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="56b9e-498">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamalara aşinaysanız:</span><span class="sxs-lookup"><span data-stu-id="56b9e-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="56b9e-499">Önceki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer görünüyor.</span><span class="sxs-lookup"><span data-stu-id="56b9e-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="56b9e-500">[Model bağlama,](xref:mvc/models/model-binding) [doğrulama,](xref:mvc/models/validation) [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi MVC ilkel lerin çoğu paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="56b9e-501">Önceki `OnPostAsync` yöntem:</span><span class="sxs-lookup"><span data-stu-id="56b9e-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="56b9e-502">Temel `OnPostAsync`akış:</span><span class="sxs-lookup"><span data-stu-id="56b9e-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="56b9e-503">Doğrulama hatalarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-503">Check for validation errors.</span></span>

* <span data-ttu-id="56b9e-504">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="56b9e-505">Hatalar varsa, sayfayı doğrulama iletileriyle yeniden gösterin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="56b9e-506">İstemci tarafı doğrulama, geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="56b9e-507">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="56b9e-508">Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için `RedirectToPageResult`yardımcı yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="56b9e-509">`RedirectToPage`yeni bir eylem sonucudur, benzer `RedirectToAction` veya `RedirectToRoute`, ancak sayfalar için özelleştirilmiş.</span><span class="sxs-lookup"><span data-stu-id="56b9e-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="56b9e-510">Önceki örnekte, kök Dizin sayfasına yönlendirir`/Index`( ).</span><span class="sxs-lookup"><span data-stu-id="56b9e-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="56b9e-511">`RedirectToPage`Sayfalar için [URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="56b9e-512">Gönderilen formda doğrulama hataları (sunucuya geçirilen) olduğunda,`OnPostAsync` işleyici `Page` yöntemi yardımcı yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="56b9e-513">`Page`, bir `PageResult` örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="56b9e-514">İade, `Page` denetleyicideki eylemlerin nasıl geri `View`döndüğüne benzer.</span><span class="sxs-lookup"><span data-stu-id="56b9e-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="56b9e-515">`PageResult`işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="56b9e-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="56b9e-516">Sayfayı döndüren `void` bir işleyici yöntemi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="56b9e-517">Özellik, `Customer` `[BindProperty]` bağlamayı modellemek için öznitelik kullanır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="56b9e-518">Razor Pages, varsayılan olarak, yalnızca`GET` olmayan fiillerle özellikleri bağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="56b9e-519">Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="56b9e-520">Bağlama, form alanlarını işlemek için aynı`<input asp-for="Customer.Name">`özelliği kullanarak kodu azaltır ( ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="56b9e-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="56b9e-521">Ana sayfa (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="56b9e-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="56b9e-522">İlişkili `PageModel` sınıf (*Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="56b9e-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="56b9e-523">*Index.cshtml* dosyası, her kişi için bir edit bağlantısı oluşturmak için aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="56b9e-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Çapa Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-route-{value}` Edit sayfasına bir bağlantı oluşturmak için özniteliği kullandı.</span><span class="sxs-lookup"><span data-stu-id="56b9e-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="56b9e-525">Bağlantı, ilgili kişi kimliğiyle birlikte rota verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="56b9e-526">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="56b9e-527">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="56b9e-528">Tag Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="56b9e-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="56b9e-529">*Sayfalar/Edit.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="56b9e-530">İlk satır yönergeyi `@page "{id:int}"` içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="56b9e-531">Yönlendirme kısıtlaması,`"{id:int}"` sayfaya rota verilerini içeren `int` sayfadaki istekleri kabul etmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="56b9e-532">Sayfaya yapılan bir `int`istek, bir ,çalışma zamanı http 404 (bulunamadı) hatasına dönüştürülebilecek rota verileri içermiyorsa.</span><span class="sxs-lookup"><span data-stu-id="56b9e-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="56b9e-533">Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:</span><span class="sxs-lookup"><span data-stu-id="56b9e-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="56b9e-534">*Sayfalar/Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="56b9e-535">*Index.cshtml* dosyası, her müşteri ilgili kişi için bir silme düğmesi oluşturmak için işaretleme de içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="56b9e-536">Silme düğmesi HTML'de işlendiğinde, `formaction` aşağıdakiparametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="56b9e-537">Öznitelik tarafından `asp-route-id` belirtilen müşteri iletişim kimliği.</span><span class="sxs-lookup"><span data-stu-id="56b9e-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="56b9e-538">Öznitelik `handler` tarafından `asp-page-handler` belirtilen.</span><span class="sxs-lookup"><span data-stu-id="56b9e-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="56b9e-539">Burada bir müşteri iletişim kimliği ile işlenen silme `1`düğmesine bir örnek:</span><span class="sxs-lookup"><span data-stu-id="56b9e-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="56b9e-540">Düğme seçildiğinde, sunucuya `POST` bir form isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="56b9e-541">Kural kuralına göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="56b9e-542">Bu `handler` örnekte olduğundan, `delete` `OnPostDeleteAsync` `POST` işleyici yöntemi isteği işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="56b9e-543">Farklı `asp-page-handler` bir değere ayarlanmışsa, `remove`örneğin , adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="56b9e-544">Aşağıdaki kod işleyiciyi `OnPostDeleteAsync` gösterir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="56b9e-545">Yöntem: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="56b9e-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="56b9e-546">Sorgu dizesinden kabul `id` eder.</span><span class="sxs-lookup"><span data-stu-id="56b9e-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="56b9e-547">*Index.cshtml* sayfa yönergesi yönlendirme `"{id:int?}"` `id` kısıtlaması içeriyorsa, rota verilerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="56b9e-548">Bunun için `id` rota verileri URI'de `https://localhost:5001/Customers/2`belirtilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="56b9e-549">Müşteri ile ilgili iletişim için `FindAsync`veritabanını sorgular.</span><span class="sxs-lookup"><span data-stu-id="56b9e-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="56b9e-550">Müşteri ilgili kişi bulunursa, müşteri ilgili kişiler listesinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="56b9e-551">Veritabanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-551">The database is updated.</span></span>
* <span data-ttu-id="56b9e-552">Kök `RedirectToPage` Dizin sayfasına yönlendirmek`/Index`için çağrılar ( ).</span><span class="sxs-lookup"><span data-stu-id="56b9e-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="56b9e-553">Sayfa özelliklerini gerektiği gibi işaretleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-553">Mark page properties as required</span></span>

<span data-ttu-id="56b9e-554">A'daki `PageModel` özellikler [Gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelik ile işaretlenebilir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="56b9e-555">Daha fazla bilgi için [Model doğrulamaya](xref:mvc/models/validation)bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="56b9e-556">OnGet işleyicisi geri dönüşle HEAD isteklerini işleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="56b9e-557">`HEAD`istekler, belirli bir kaynak için üstbilgi almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="56b9e-558">İsteklerden `GET` `HEAD` farklı olarak, istekler yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="56b9e-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="56b9e-559">Normalde, bir `OnHead` işleyici oluşturulur ve istekler için `HEAD` çağrılır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="56b9e-560">Core 2.1 veya daha sonraki ASP.NET, Razor `OnGet` Pages hiçbir `OnHead` işleyici tanımlı ise işleyici aramaya geri düşer.</span><span class="sxs-lookup"><span data-stu-id="56b9e-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="56b9e-561">Bu davranış, [SetCompatibilityVersion](xref:mvc/compatibility-version) için çağrı `Startup.ConfigureServices`tarafından etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="56b9e-562">Varsayılan şablonlar çağrıyı `SetCompatibilityVersion` Core 2.1 ve 2.2'ASP.NET oluşturur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="56b9e-563">`SetCompatibilityVersion`razor Pages seçeneğini `AllowMappingHeadRequestsToGetHandler` etkin `true`bir şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="56b9e-564">Tüm davranışları seçerek tercih etmek `SetCompatibilityVersion`yerine, *açıkça belirli* davranışları tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="56b9e-565">Aşağıdaki kod, isteklerin `HEAD` `OnGet` işleyiciye eşlenemesine izin vermeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="56b9e-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="56b9e-566">XSRF/CSRF ve Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="56b9e-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="56b9e-567">[Antiforgery doğrulama](xref:security/anti-request-forgery)için herhangi bir kod yazmak zorunda değilsiniz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="56b9e-568">Antiforgery belirteç oluşturma ve doğrulama otomatik olarak Razor Pages dahildir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="56b9e-569">Düzenleri, kısmileri, şablonları ve Yardımcıları Jilet Li Sayfalarla Etiketleme</span><span class="sxs-lookup"><span data-stu-id="56b9e-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="56b9e-570">Sayfalar, Razor görünüm motorunun tüm özellikleriyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="56b9e-571">Düzenleri, partials, şablonlar, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* geleneksel Razor görünümleri için yaptıkları gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="56b9e-572">Bu özelliklerden bazılarını yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="56b9e-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="56b9e-573">*Sayfalara/Paylaşılan/_Layout.cshtml'e* [düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="56b9e-574">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="56b9e-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="56b9e-575">Her sayfanın düzenini denetler (sayfa mizanpajı devre dışı bırakmadığı sürece).</span><span class="sxs-lookup"><span data-stu-id="56b9e-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="56b9e-576">JavaScript ve stylesheets gibi HTML yapılarını içeri aktlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="56b9e-577">Daha fazla bilgi için [düzen sayfasına](xref:mvc/views/layout) bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="56b9e-578">[Düzen](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart.cshtml*olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="56b9e-579">Düzen *Sayfalar/Paylaşılanklasöründedir.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="56b9e-580">Sayfalar, geçerli sayfayla aynı klasörden başlayarak hiyerarşik olarak diğer görünümleri (düzenler, şablonlar, kısmiler) arar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="56b9e-581">*Sayfalar/Paylaşılan* klasöründeki bir *düzen, Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="56b9e-582">Düzen dosyası *Sayfalar/Paylaşılan* klasörüne gitmeli.</span><span class="sxs-lookup"><span data-stu-id="56b9e-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="56b9e-583">Düzen dosyasını *Görünümler/Paylaşılanklasörüne* **koymamanızı** öneririz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="56b9e-584">*Görünümler/Paylaşılanlar* bir MVC görünüm desenidir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="56b9e-585">Razor Pages, yol kurallarına değil, klasör hiyerarşisine güvenmek içindir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="56b9e-586">Razor Page'deki aramayı *görüntüleyin, Sayfalar* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="56b9e-587">MVC denetleyicileri ve geleneksel Razor görünümleri ile kullandığınız mizanpajlar, şablonlar ve kısmi ler *yalnızca çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="56b9e-588">*Pages/_ViewImports.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="56b9e-589">`@namespace`öğreticidaha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="56b9e-590">Yönerge, `@addTagHelper` yerleşik Tag *Yardımcıları'nı Sayfalar* klasöründeki tüm sayfalara getirir. [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index)</span><span class="sxs-lookup"><span data-stu-id="56b9e-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="56b9e-591">`@namespace` Yönerge bir sayfada açıkça kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="56b9e-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="56b9e-592">Yönerge, sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="56b9e-593">Yönerge, `@model` ad alanını içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="56b9e-594">Yönerge *_ViewImports.cshtml'de*yer aldığında, belirtilen ad `@namespace` alanı, yönergeyi içeri yediren Sayfada oluşturulan ad alanı için öneki sağlar. `@namespace`</span><span class="sxs-lookup"><span data-stu-id="56b9e-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="56b9e-595">Oluşturulan ad alanının geri kalanı (sonek bölümü), *_ViewImports.cshtml* içeren klasör ile sayfayı içeren klasör arasındaki nokta-ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="56b9e-596">Örneğin, `PageModel` Sınıf *Pages/Customers/Edit.cshtml.cs* açıkça ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="56b9e-597">*Pages/_ViewImports.cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="56b9e-598">*Sayfalar/Müşteriler/Edit.cshtml* Razor Page için oluşturulan ad alanı `PageModel` sınıfla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="56b9e-599">`@namespace`*ayrıca geleneksel Razor görünümleri ile çalışır.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="56b9e-600">Özgün *Pages/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="56b9e-601">Güncelleştirilmiş *Sayfalar/Create.cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="56b9e-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="56b9e-602">[Razor Pages başlangıç projesi,](#rpvs17) istemci tarafı doğrulaması kancasayfalar/_ValidationScriptsPartial.cshtml içerir. *Pages/_ValidationScriptsPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="56b9e-603">Kısmi görünümler hakkında daha <xref:mvc/views/partial>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="56b9e-604">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="56b9e-604">URL generation for Pages</span></span>

<span data-ttu-id="56b9e-605">Daha `Create` önce gösterilen sayfa `RedirectToPage`yı kullanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="56b9e-606">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="56b9e-607">*/Sayfalar*</span><span class="sxs-lookup"><span data-stu-id="56b9e-607">*/Pages*</span></span>

  * <span data-ttu-id="56b9e-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="56b9e-609">*/Müşteriler*</span><span class="sxs-lookup"><span data-stu-id="56b9e-609">*/Customers*</span></span>

    * <span data-ttu-id="56b9e-610">*Oluştur.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="56b9e-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="56b9e-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56b9e-612">*Index.cshtml*</span></span>

<span data-ttu-id="56b9e-613">*Sayfalar/Müşteriler/Create.cshtml* ve *Pages/Customers/Edit.cshtml* sayfaları başarıdan sonra *Pages/Index.cshtml'e* yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="56b9e-614">Dize, `/Index` önceki sayfaya erişmek için URI'nin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="56b9e-615">`/Index` *Dize, Pages/Index.cshtml* sayfasına ÜRBİ oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="56b9e-616">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="56b9e-617">Sayfa adı, bir satır aralığı `/` (örneğin, `/Index`) dahil olmak üzere kök */Sayfalar* klasöründen sayfaya giden yoldur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="56b9e-618">Önceki URL oluşturma örnekleri, bir URL'yi kodlamakonusunda gelişmiş seçenekler ve işlevsel özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="56b9e-619">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve rotanın hedef yolda nasıl tanımlandığına göre parametreler oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="56b9e-620">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="56b9e-621">Aşağıdaki tabloda, `RedirectToPage` *Sayfalar/Müşteriler/Create.cshtml*farklı parametrelerle hangi Dizin sayfasının seçildiği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="56b9e-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="56b9e-622">YönlendirmePage(x)</span><span class="sxs-lookup"><span data-stu-id="56b9e-622">RedirectToPage(x)</span></span>| <span data-ttu-id="56b9e-623">Sayfa</span><span class="sxs-lookup"><span data-stu-id="56b9e-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="56b9e-624">ReDirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="56b9e-625">*Sayfalar/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-625">*Pages/Index*</span></span> |
| <span data-ttu-id="56b9e-626">ReDirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="56b9e-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="56b9e-627">*Sayfalar/Müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="56b9e-628">YönlendirmePage("... /Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="56b9e-629">*Sayfalar/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-629">*Pages/Index*</span></span> |
| <span data-ttu-id="56b9e-630">ReDirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="56b9e-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="56b9e-631">*Sayfalar/Müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="56b9e-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="56b9e-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` *göreli adlar*.</span><span class="sxs-lookup"><span data-stu-id="56b9e-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="56b9e-633">Parametre, `RedirectToPage` hedef sayfanın adını hesaplamak için geçerli sayfanın yolu ile *birleştirilir.*</span><span class="sxs-lookup"><span data-stu-id="56b9e-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="56b9e-634">Göreli ad bağlama, karmaşık bir yapıya sahip siteleri inşa ederken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="56b9e-635">Bir klasördeki sayfalar arasında bağlantı vermek için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="56b9e-636">Tüm bağlantılar hala çalışır (klasör adını içermedikleri için).</span><span class="sxs-lookup"><span data-stu-id="56b9e-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="56b9e-637">Farklı bir [Alan'daki](xref:mvc/controllers/areas)bir sayfaya yönlendirmek için alanı belirtin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="56b9e-638">Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="56b9e-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="56b9e-639">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="56b9e-639">ViewData attribute</span></span>

<span data-ttu-id="56b9e-640">Veriler [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)ile bir sayfaya geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="56b9e-641">Öznitelikile `[ViewData]` denetleyicilerin veya Razor Page modellerindeki özelliklerin değerleri [ViewDataDictionary'den](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="56b9e-642">Aşağıdaki örnekte, `AboutModel` `[ViewData]`'ile `Title` işaretlenmiş bir özellik içerir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="56b9e-643">Özellik, `Title` Hakkında sayfanın başlığına ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="56b9e-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="56b9e-644">Hakkında sayfasında, `Title` özellik bir model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="56b9e-645">Düzende, başlık ViewData sözlüğünden okunur:</span><span class="sxs-lookup"><span data-stu-id="56b9e-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="56b9e-646">Geçici Veriler</span><span class="sxs-lookup"><span data-stu-id="56b9e-646">TempData</span></span>

<span data-ttu-id="56b9e-647">ASP.NET Core, [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller)ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="56b9e-648">Bu özellik, verileri okunana kadar depolar.</span><span class="sxs-lookup"><span data-stu-id="56b9e-648">This property stores data until it's read.</span></span> <span data-ttu-id="56b9e-649">Ve `Keep` `Peek` yöntemleri silme olmadan verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="56b9e-650">`TempData`tek bir istekten daha fazlası için veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="56b9e-651">Aşağıdaki kod `Message` kullanma `TempData`değerini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="56b9e-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="56b9e-652">*Sayfalar/Müşteriler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirme, `Message` kullanmanın `TempData`değerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="56b9e-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="56b9e-653">*Sayfalar/Müşteriler/Index.cshtml.cs* sayfa modeli `[TempData]` `Message` özelliğiöznitelik uygular.</span><span class="sxs-lookup"><span data-stu-id="56b9e-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="56b9e-654">Daha fazla bilgi için [TempData'ya](xref:fundamentals/app-state#tempdata) bakın.</span><span class="sxs-lookup"><span data-stu-id="56b9e-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="56b9e-655">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="56b9e-655">Multiple handlers per page</span></span>

<span data-ttu-id="56b9e-656">Aşağıdaki sayfa, `asp-page-handler` Etiket Yardımcısı'nı kullanarak iki işleyici için biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="56b9e-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="56b9e-657">Önceki örnekteki formun her biri farklı bir `FormActionTagHelper` URL'ye göndermek için kullanılan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="56b9e-658">Öznitelik `asp-page-handler` bir `asp-page`arkadaşıdır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="56b9e-659">`asp-page-handler`bir sayfa tarafından tanımlanan işleyici yöntemlerinin her birine gönderen URL'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="56b9e-660">`asp-page`örnek geçerli sayfaya bağlandığı için belirtilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="56b9e-661">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="56b9e-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="56b9e-662">Önceki kod *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="56b9e-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="56b9e-663">Adlandırılmış işleyici yöntemleri, (varsa) sonra `On<HTTP Verb>` ve `Async` öncesinde ki addaki metin alınarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="56b9e-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="56b9e-664">Önceki örnekte, sayfa yöntemleri OnPost**JoinList**Async ve OnPost**JoinListUC**Async'dir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="56b9e-665">*OnPost* ve *Async* kaldırıldı, işleyici `JoinList` `JoinListUC`adları ve .</span><span class="sxs-lookup"><span data-stu-id="56b9e-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="56b9e-666">Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH?handler=JoinList`</span><span class="sxs-lookup"><span data-stu-id="56b9e-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="56b9e-667">Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="56b9e-668">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="56b9e-668">Custom routes</span></span>

<span data-ttu-id="56b9e-669">Yönergeyi `@page` şu şekilde kullanın:</span><span class="sxs-lookup"><span data-stu-id="56b9e-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="56b9e-670">Sayfaya özel bir rota belirtin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-670">Specify a custom route to a page.</span></span> <span data-ttu-id="56b9e-671">Örneğin, Hakkında sayfasına `/Some/Other/Path` giden yol . `@page "/Some/Other/Path"`</span><span class="sxs-lookup"><span data-stu-id="56b9e-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="56b9e-672">Bir sayfanın varsayılan rotasına segmentleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-672">Append segments to a page's default route.</span></span> <span data-ttu-id="56b9e-673">Örneğin, bir "öğe" kesimi sayfanın varsayılan rotasına `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="56b9e-674">Parametreleri sayfanın varsayılan rotasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="56b9e-675">Örneğin, bir kimlik parametresi, `id`, . `@page "{id}"`</span><span class="sxs-lookup"><span data-stu-id="56b9e-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="56b9e-676">Yolun başında ki bir tilde`~`( ) tarafından belirlenen kök-göreli yol desteklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="56b9e-677">Örneğin, `@page "~/Some/Other/Path"` aynı `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="56b9e-678">URL'deki sorgu dizesini `?handler=JoinList` beğenmezseniz, URL'nin yol bölümüne işleyici adını koymak için rotayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="56b9e-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="56b9e-679">Rota, `@page` yönergeden sonra çift tırnak içinde bir rota şablonu eklenerek özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="56b9e-680">Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH/JoinList`</span><span class="sxs-lookup"><span data-stu-id="56b9e-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="56b9e-681">Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="56b9e-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="56b9e-682">`?` Aşağıdaki, `handler` rota parametresi isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="56b9e-683">Yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="56b9e-683">Configuration and settings</span></span>

<span data-ttu-id="56b9e-684">Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC oluşturucuüzerinde uzantı yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="56b9e-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="56b9e-685">Şu anda `RazorPagesOptions` sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56b9e-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="56b9e-686">Gelecekte daha fazla genişletilebilirlik sağlayacağız.</span><span class="sxs-lookup"><span data-stu-id="56b9e-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="56b9e-687">Görünümleri önceden derlemek [için, Bkz. Razor görünüm](xref:mvc/views/view-compilation) derlemesi.</span><span class="sxs-lookup"><span data-stu-id="56b9e-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="56b9e-688">[Örnek kodu indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)</span><span class="sxs-lookup"><span data-stu-id="56b9e-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="56b9e-689">Bkz. Bu giriş üzerine inşa edilen [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="56b9e-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="56b9e-690">Razor Sayfalarının içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="56b9e-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="56b9e-691">Varsayılan olarak, Razor Pages */Pages* dizininde köklenir.</span><span class="sxs-lookup"><span data-stu-id="56b9e-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="56b9e-692">Razor Sayfalarınızın uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) [(ContentRootPath)](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)olduğunu belirtmek için [AddMvc'ye](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="56b9e-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="56b9e-693">Razor Sayfalarının özel bir kök dizininde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="56b9e-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="56b9e-694">Razor Sayfalarınızın uygulamada özel bir kök dizininde olduğunu belirtmek için [AddMvc'ye](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin (göreceli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="56b9e-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="56b9e-695">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="56b9e-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
