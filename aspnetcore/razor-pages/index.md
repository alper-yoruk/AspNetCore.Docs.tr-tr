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
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424560"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="3160f-103">ASP.NET Core Razor Pages giriş</span><span class="sxs-lookup"><span data-stu-id="3160f-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3160f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3160f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3160f-105">Razor Pages, kodlama sayfasına odaklanmış senaryolar denetleyicileri ve görünümleri kullanmaktan daha kolay ve daha üretken hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3160f-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="3160f-106">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="3160f-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="3160f-107">Bu belge Razor Pages bir giriş sağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="3160f-108">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="3160f-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="3160f-109">Bölümlerden bazılarını çok gelişmiş bir şekilde bulursanız, bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3160f-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="3160f-110">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="3160f-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3160f-111">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3160f-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3160f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3160f-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3160f-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3160f-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="3160f-115">Razor Pages projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="3160f-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3160f-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3160f-117">Razor Pages projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3160f-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3160f-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3160f-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3160f-119">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3160f-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3160f-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3160f-121">Razor Pages projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3160f-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="3160f-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3160f-122">Razor Pages</span></span>

<span data-ttu-id="3160f-123">Razor Pages, *Startup.cs*'de etkinleştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3160f-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="3160f-124">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3160f-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="3160f-125">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi bir çok şey arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="3160f-126">Bu, farklı kılan [`@page`](xref:mvc/views/razor#page) yönergedir.</span><span class="sxs-lookup"><span data-stu-id="3160f-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="3160f-127">`@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3160f-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3160f-128">`@page`sayfada ilk Razor yönergesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3160f-129">`@page`diğer [Razor](xref:mvc/views/razor) yapıları davranışını etkiler.</span><span class="sxs-lookup"><span data-stu-id="3160f-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="3160f-130">Razor Pages dosya adlarında *. cshtml* soneki vardır.</span><span class="sxs-lookup"><span data-stu-id="3160f-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="3160f-131">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3160f-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3160f-132">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3160f-133">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3160f-134">Kurala göre, `PageModel` sınıf dosyası *. cs* eklenmiş Razor sayfası dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3160f-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3160f-135">Örneğin, önceki Razor sayfası *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3160f-136">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3160f-137">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3160f-138">Aşağıdaki tabloda bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3160f-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3160f-139">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="3160f-139">File name and path</span></span>               | <span data-ttu-id="3160f-140">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="3160f-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3160f-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3160f-142">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="3160f-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="3160f-143">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="3160f-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3160f-144">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="3160f-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3160f-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3160f-146">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3160f-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3160f-147">Notlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-147">Notes:</span></span>

* <span data-ttu-id="3160f-148">Çalışma zamanı, *Sayfalar* klasöründeki Razor Pages dosyaları varsayılan olarak arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3160f-149">`Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="3160f-150">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="3160f-150">Write a basic form</span></span>

<span data-ttu-id="3160f-151">Razor Pages, Web tarayıcıları ile kullanılan ortak desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="3160f-152">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi, Razor sayfası sınıfında tanımlanan özelliklerle *çalışır* .</span><span class="sxs-lookup"><span data-stu-id="3160f-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3160f-153">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="3160f-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3160f-154">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="3160f-155">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="3160f-156">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="3160f-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="3160f-157">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="3160f-158">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3160f-159">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="3160f-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3160f-160">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="3160f-161">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="3160f-162">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-162">This separation allows:</span></span>

* <span data-ttu-id="3160f-163">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="3160f-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="3160f-164">Birim testi</span><span class="sxs-lookup"><span data-stu-id="3160f-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="3160f-165">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="3160f-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3160f-166">Herhangi bir HTTP fiili için işleyici metotları eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="3160f-167">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3160f-167">The most common handlers are:</span></span>

* <span data-ttu-id="3160f-168">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="3160f-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3160f-169">Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* Razor sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3160f-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="3160f-170">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="3160f-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3160f-171">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3160f-172">Yukarıdaki kod Razor Pages için tipik bir davranıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="3160f-173">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="3160f-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="3160f-174">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="3160f-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="3160f-175">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve Razor Pages aynı şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="3160f-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="3160f-176">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="3160f-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3160f-177">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="3160f-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3160f-178">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="3160f-178">Check for validation errors.</span></span>

* <span data-ttu-id="3160f-179">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="3160f-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="3160f-180">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3160f-181">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3160f-182">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="3160f-183">Sayfalardan işlenmiş HTML */Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3160f-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="3160f-184">Önceki kodda, formu deftere nakletme:</span><span class="sxs-lookup"><span data-stu-id="3160f-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="3160f-185">Geçerli verilerle:</span><span class="sxs-lookup"><span data-stu-id="3160f-185">With valid data:</span></span>

  * <span data-ttu-id="3160f-186">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="3160f-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="3160f-187">`RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="3160f-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="3160f-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="3160f-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="3160f-189">Bir eylem sonucudur.</span><span class="sxs-lookup"><span data-stu-id="3160f-189">Is an action result.</span></span>
    * <span data-ttu-id="3160f-190">, Veya ile `RedirectToAction` benzerdir `RedirectToRoute` (denetleyiciler ve görünümlerde kullanılır).</span><span class="sxs-lookup"><span data-stu-id="3160f-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="3160f-191">Sayfalar için özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-191">Is customized for pages.</span></span> <span data-ttu-id="3160f-192">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3160f-193">`RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="3160f-194">Sunucuya geçirilen doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="3160f-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="3160f-195">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="3160f-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="3160f-196">`Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="3160f-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="3160f-197">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="3160f-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3160f-198">`PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="3160f-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="3160f-199">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="3160f-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="3160f-200">Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="3160f-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="3160f-201">Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler.</span><span class="sxs-lookup"><span data-stu-id="3160f-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="3160f-202">Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="3160f-203">İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="3160f-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="3160f-204">Veriler sunucuya **nakledilmedi.**</span><span class="sxs-lookup"><span data-stu-id="3160f-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="3160f-205">İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="3160f-206">`Customer`Özelliği, [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) model bağlamasını kabul etmek için özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="3160f-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="3160f-207">`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="3160f-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="3160f-208">Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="3160f-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="3160f-209">Razor Pages, varsayılan olarak yalnızca fiiller olmayan özellikleri bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="3160f-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="3160f-210">Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3160f-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="3160f-211">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="3160f-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="3160f-212">*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:</span><span class="sxs-lookup"><span data-stu-id="3160f-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="3160f-213">Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="3160f-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Etiket Yardımcıları kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3160f-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="3160f-215">Giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="3160f-215">The home page</span></span>

<span data-ttu-id="3160f-216">*Index. cshtml* giriş sayfasıdır:</span><span class="sxs-lookup"><span data-stu-id="3160f-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="3160f-217">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3160f-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3160f-218">*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="3160f-219">`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="3160f-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3160f-220">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3160f-221">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="3160f-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="3160f-222">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="3160f-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="3160f-223">*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="3160f-224">İşlenmiş HTML:</span><span class="sxs-lookup"><span data-stu-id="3160f-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="3160f-225">Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="3160f-226">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="3160f-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="3160f-227">, `handler` Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="3160f-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="3160f-228">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="3160f-229">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="3160f-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="3160f-230">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="3160f-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="3160f-231">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="3160f-232">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3160f-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="3160f-233">`id`Sorgu dizesinden alır.</span><span class="sxs-lookup"><span data-stu-id="3160f-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="3160f-234">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="3160f-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="3160f-235">Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="3160f-236"><xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="3160f-237">Edit. cshtml dosyası</span><span class="sxs-lookup"><span data-stu-id="3160f-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="3160f-238">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3160f-239">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="3160f-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3160f-240">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="3160f-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3160f-241">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3160f-242">*Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="3160f-243">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="3160f-243">Validation</span></span>

<span data-ttu-id="3160f-244">Doğrulama kuralları:</span><span class="sxs-lookup"><span data-stu-id="3160f-244">Validation rules:</span></span>

* <span data-ttu-id="3160f-245">Model sınıfında bildirimli olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="3160f-246">Uygulamada her yerde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="3160f-247"><xref:System.ComponentModel.DataAnnotations>Ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="3160f-248">Dataaçıklamalarda, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.</span><span class="sxs-lookup"><span data-stu-id="3160f-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="3160f-249">Modeli göz önünde bulundurun `Customer` :</span><span class="sxs-lookup"><span data-stu-id="3160f-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="3160f-250">Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:</span><span class="sxs-lookup"><span data-stu-id="3160f-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="3160f-251">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="3160f-251">The preceding code:</span></span>

* <span data-ttu-id="3160f-252">JQuery ve jQuery doğrulama betikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="3160f-253">`<div />`' İ `<span />` etkinleştirmek için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:</span><span class="sxs-lookup"><span data-stu-id="3160f-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="3160f-254">İstemci tarafı doğrulama.</span><span class="sxs-lookup"><span data-stu-id="3160f-254">Client-side validation.</span></span>
  * <span data-ttu-id="3160f-255">Doğrulama hatası işleme.</span><span class="sxs-lookup"><span data-stu-id="3160f-255">Validation error rendering.</span></span>

* <span data-ttu-id="3160f-256">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3160f-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="3160f-257">Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3160f-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="3160f-258">formunda.</span><span class="sxs-lookup"><span data-stu-id="3160f-258">on the form.</span></span> <span data-ttu-id="3160f-259">İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3160f-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="3160f-260">`[StringLength(10)]`Özniteliği `data-val-length-max="10"` işlenmiş html üzerinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3160f-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="3160f-261">`data-val-length-max`tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="3160f-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="3160f-262">Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:</span><span class="sxs-lookup"><span data-stu-id="3160f-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="3160f-263">, Adı 10 ' dan daha uzun.</span><span class="sxs-lookup"><span data-stu-id="3160f-263">With the name longer than 10.</span></span>
* <span data-ttu-id="3160f-264">"Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="3160f-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="3160f-265">döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3160f-265">is returned.</span></span>

<span data-ttu-id="3160f-266">Aşağıdaki modeli göz önünde bulundurun `Movie` :</span><span class="sxs-lookup"><span data-stu-id="3160f-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="3160f-267">Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="3160f-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="3160f-268">`Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="3160f-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="3160f-269">`RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="3160f-270">Yukarıdaki kodda, "tarz":</span><span class="sxs-lookup"><span data-stu-id="3160f-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="3160f-271">Yalnızca harfler kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-271">Must only use letters.</span></span>
  * <span data-ttu-id="3160f-272">İlk harfin büyük harfle olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3160f-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="3160f-273">Boşluk, sayı ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="3160f-274">`RegularExpression`"Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="3160f-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="3160f-275">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3160f-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="3160f-276">Sonraki boşlukların içindeki özel karakter ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="3160f-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="3160f-277">"PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="3160f-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="3160f-278">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="3160f-279">`StringLength`Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="3160f-280">Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="3160f-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="3160f-281">Model için Oluştur sayfasında, `Movie` geçersiz değerlere sahip hatalar görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3160f-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="3160f-283">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="3160f-283">For more information, see:</span></span>

* [<span data-ttu-id="3160f-284">Film uygulamasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="3160f-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="3160f-285">[ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3160f-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="3160f-286">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="3160f-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="3160f-287">`HEAD`istekler belirli bir kaynak için üstbilgileri almaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="3160f-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="3160f-288">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="3160f-289">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="3160f-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="3160f-290">Razor Pages, işleyici `OnGet` tanımlanmadığında işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="3160f-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3160f-291">XSRF/CSRF ve Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3160f-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3160f-292">Razor Pages, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur.</span><span class="sxs-lookup"><span data-stu-id="3160f-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3160f-293">[Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="3160f-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3160f-294">Razor Pages ile düzenleri, partileri, şablonları ve etiket yardımcılarını kullanma</span><span class="sxs-lookup"><span data-stu-id="3160f-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3160f-295">Sayfalar, Razor görünüm altyapısının tüm özellikleri ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="3160f-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="3160f-296">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*ve *_ViewImports. cshtml* geleneksel Razor görünümlerinde oldukları gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="3160f-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3160f-297">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="3160f-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="3160f-298">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="3160f-299">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="3160f-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3160f-300">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="3160f-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3160f-301">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="3160f-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="3160f-302">Razor sayfasının içerikleri, `@RenderBody()` çağrıldığında işlenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="3160f-303">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="3160f-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="3160f-304">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3160f-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="3160f-305">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="3160f-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="3160f-306">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3160f-307">*Sayfalar/paylaşılan* klasördeki bir düzen, *Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3160f-308">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="3160f-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="3160f-309">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="3160f-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3160f-310">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="3160f-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3160f-311">Razor Pages, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="3160f-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3160f-312">Bir Razor sayfasından arama görüntüleme, *Sayfalar* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3160f-313">MVC denetleyicileri ve geleneksel Razor görünümleriyle kullanılan düzenler, şablonlar ve partilar *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="3160f-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3160f-314">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3160f-315">`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3160f-316">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="3160f-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3160f-317">`@namespace`Bir sayfada ayarlanan yönerge:</span><span class="sxs-lookup"><span data-stu-id="3160f-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3160f-318">`@namespace`Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="3160f-319">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3160f-320">`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="3160f-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3160f-321">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="3160f-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3160f-322">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3160f-323">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3160f-324">*Pages/Customers/Edit. cshtml* Razor sayfasının oluşturulan ad alanı `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="3160f-325">`@namespace`*Ayrıca geleneksel Razor görünümleriyle birlikte da geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="3160f-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="3160f-326">*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3160f-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="3160f-327">Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="3160f-328">Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="3160f-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="3160f-329">Düzen dosyası JavaScript dosyalarını içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="3160f-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="3160f-330">[Razor Pages Starter projesi](#rpvs17) , istemci tarafı doğrulamayı bağlayan *sayfaları/_ValidationScriptsPartial. cshtml*'yi içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="3160f-331">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="3160f-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3160f-332">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="3160f-332">URL generation for Pages</span></span>

<span data-ttu-id="3160f-333">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="3160f-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="3160f-334">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="3160f-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3160f-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="3160f-335">*/Pages*</span></span>

  * <span data-ttu-id="3160f-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="3160f-337">*Gizlilik. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="3160f-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="3160f-338">*/Customers*</span></span>

    * <span data-ttu-id="3160f-339">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="3160f-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="3160f-340">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3160f-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-341">*Index.cshtml*</span></span>

<span data-ttu-id="3160f-342">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="3160f-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="3160f-343">Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="3160f-344">*Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="3160f-345">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3160f-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="3160f-346">Mutlak sayfa adı, `/Index` *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3160f-347">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3160f-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3160f-348">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="3160f-349">Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="3160f-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="3160f-350">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3160f-351">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="3160f-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3160f-352">Aşağıdaki tabloda, `RedirectToPage` *sayfalarda/müşteriler/Create. cshtml*'de farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3160f-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="3160f-353">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="3160f-353">RedirectToPage(x)</span></span>| <span data-ttu-id="3160f-354">Sayfa</span><span class="sxs-lookup"><span data-stu-id="3160f-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3160f-355">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="3160f-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3160f-356">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-356">*Pages/Index*</span></span> |
| <span data-ttu-id="3160f-357">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="3160f-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3160f-358">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3160f-359">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="3160f-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3160f-360">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-360">*Pages/Index*</span></span> |
| <span data-ttu-id="3160f-361">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="3160f-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3160f-362">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="3160f-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="3160f-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="3160f-364">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="3160f-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="3160f-365">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="3160f-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3160f-366">Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="3160f-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="3160f-367">Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.</span><span class="sxs-lookup"><span data-stu-id="3160f-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="3160f-368">Klasör adını içermediği için bağlantılar kopuk değildir.</span><span class="sxs-lookup"><span data-stu-id="3160f-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="3160f-369">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="3160f-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="3160f-370">Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="3160f-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="3160f-371">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="3160f-371">ViewData attribute</span></span>

<span data-ttu-id="3160f-372">Veri, ile bir sayfaya geçirilebilir <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="3160f-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="3160f-373">Özniteliği olan Özellikler `[ViewData]` , değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="3160f-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="3160f-374">Aşağıdaki örnekte, `AboutModel` `[ViewData]` özniteliği `Title` özelliğine uygular:</span><span class="sxs-lookup"><span data-stu-id="3160f-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="3160f-375">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="3160f-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="3160f-376">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="3160f-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="3160f-377">TempData</span><span class="sxs-lookup"><span data-stu-id="3160f-377">TempData</span></span>

<span data-ttu-id="3160f-378">ASP.NET Core, öğesini kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="3160f-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="3160f-379">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="3160f-379">This property stores data until it's read.</span></span> <span data-ttu-id="3160f-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3160f-381">`TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3160f-382">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="3160f-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3160f-383">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="3160f-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3160f-384">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="3160f-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3160f-385">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="3160f-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3160f-386">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="3160f-386">Multiple handlers per page</span></span>

<span data-ttu-id="3160f-387">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="3160f-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="3160f-388">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="3160f-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3160f-389">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3160f-390">`asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3160f-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3160f-391">`asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="3160f-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3160f-392">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3160f-393">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3160f-394">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="3160f-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3160f-395">Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3160f-396">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="3160f-397">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="3160f-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3160f-398">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="3160f-399">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="3160f-399">Custom routes</span></span>

<span data-ttu-id="3160f-400">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3160f-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="3160f-401">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="3160f-401">Specify a custom route to a page.</span></span> <span data-ttu-id="3160f-402">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="3160f-403">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-403">Append segments to a page's default route.</span></span> <span data-ttu-id="3160f-404">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="3160f-405">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="3160f-406">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="3160f-407">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="3160f-408">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="3160f-409">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3160f-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3160f-410">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="3160f-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3160f-411">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="3160f-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="3160f-412">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="3160f-413">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3160f-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="3160f-414">Gelişmiş yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="3160f-414">Advanced configuration and settings</span></span>

<span data-ttu-id="3160f-415">Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3160f-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="3160f-416">Gelişmiş seçenekleri yapılandırmak için genişletme yöntemini kullanın <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="3160f-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="3160f-417">Sayfalar için <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3160f-418">Kurallar hakkında daha fazla bilgi için bkz. [Razor Pages yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="3160f-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="3160f-419">Görünümleri önceden derlemek için bkz. [Razor görünüm derlemesi](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="3160f-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="3160f-420">Razor Pages içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="3160f-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="3160f-421">Varsayılan olarak, Razor Pages */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="3160f-422"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>Razor Pages, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) () olduğunu belirtmek için ekleyin <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> :</span><span class="sxs-lookup"><span data-stu-id="3160f-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="3160f-423">Razor Pages özel kök dizinde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="3160f-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="3160f-424"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>Razor Pages, uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="3160f-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="3160f-425">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3160f-425">Additional resources</span></span>

* <span data-ttu-id="3160f-426">Bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start), bu giriş hakkında derleme</span><span class="sxs-lookup"><span data-stu-id="3160f-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="3160f-427">Örnek kodu indirme veya görüntüleme</span><span class="sxs-lookup"><span data-stu-id="3160f-427">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="3160f-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3160f-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3160f-429">Razor Pages, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir yönüdür.</span><span class="sxs-lookup"><span data-stu-id="3160f-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="3160f-430">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="3160f-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="3160f-431">Bu belge Razor Pages bir giriş sağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="3160f-432">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="3160f-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="3160f-433">Bölümlerden bazılarını çok gelişmiş bir şekilde bulursanız, bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3160f-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="3160f-434">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="3160f-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3160f-435">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3160f-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3160f-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3160f-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3160f-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3160f-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="3160f-439">Razor Pages projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="3160f-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3160f-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3160f-441">Razor Pages projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3160f-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3160f-442">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3160f-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3160f-443">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3160f-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="3160f-444">Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.</span><span class="sxs-lookup"><span data-stu-id="3160f-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3160f-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3160f-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3160f-446">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3160f-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="3160f-447">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3160f-447">Razor Pages</span></span>

<span data-ttu-id="3160f-448">Razor Pages, *Startup.cs*'de etkinleştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3160f-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="3160f-449">Temel bir sayfa düşünün:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3160f-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="3160f-450">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi bir çok şey arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="3160f-451">Bu, farklı kılan `@page` yönergedir.</span><span class="sxs-lookup"><span data-stu-id="3160f-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="3160f-452">`@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3160f-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3160f-453">`@page`sayfada ilk Razor yönergesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3160f-454">`@page`diğer Razor yapıları davranışını etkiler.</span><span class="sxs-lookup"><span data-stu-id="3160f-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="3160f-455">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3160f-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3160f-456">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3160f-457">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3160f-458">Kurala göre, `PageModel` sınıf dosyası *. cs* eklenmiş Razor sayfası dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3160f-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3160f-459">Örneğin, önceki Razor sayfası *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3160f-460">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3160f-461">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3160f-462">Aşağıdaki tabloda bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3160f-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3160f-463">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="3160f-463">File name and path</span></span>               | <span data-ttu-id="3160f-464">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="3160f-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3160f-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3160f-466">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="3160f-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="3160f-467">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="3160f-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3160f-468">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="3160f-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3160f-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3160f-470">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3160f-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3160f-471">Notlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-471">Notes:</span></span>

* <span data-ttu-id="3160f-472">Çalışma zamanı, *Sayfalar* klasöründeki Razor Pages dosyaları varsayılan olarak arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3160f-473">`Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="3160f-474">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="3160f-474">Write a basic form</span></span>

<span data-ttu-id="3160f-475">Razor Pages, Web tarayıcıları ile kullanılan ortak desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="3160f-476">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi, Razor sayfası sınıfında tanımlanan özelliklerle *çalışır* .</span><span class="sxs-lookup"><span data-stu-id="3160f-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3160f-477">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="3160f-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3160f-478">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="3160f-479">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="3160f-480">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="3160f-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="3160f-481">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="3160f-482">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3160f-483">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="3160f-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3160f-484">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="3160f-485">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="3160f-486">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-486">This separation allows:</span></span>

* <span data-ttu-id="3160f-487">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="3160f-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="3160f-488">Sayfaların [birim testi](xref:test/razor-pages-tests) .</span><span class="sxs-lookup"><span data-stu-id="3160f-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="3160f-489">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="3160f-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3160f-490">Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3160f-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="3160f-491">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3160f-491">The most common handlers are:</span></span>

* <span data-ttu-id="3160f-492">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="3160f-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3160f-493">[OnGet](#OnGet) örneği.</span><span class="sxs-lookup"><span data-stu-id="3160f-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="3160f-494">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="3160f-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3160f-495">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3160f-496">Yukarıdaki kod Razor Pages için tipik bir davranıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="3160f-497">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="3160f-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="3160f-498">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="3160f-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="3160f-499">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="3160f-500">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="3160f-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3160f-501">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="3160f-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3160f-502">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="3160f-502">Check for validation errors.</span></span>

* <span data-ttu-id="3160f-503">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="3160f-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="3160f-504">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3160f-505">İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="3160f-506">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3160f-507">Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için yardımcı yöntemini çağırır `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="3160f-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="3160f-508">`RedirectToPage`, `RedirectToAction` veya ' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur `RedirectToRoute` .</span><span class="sxs-lookup"><span data-stu-id="3160f-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="3160f-509">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3160f-510">`RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="3160f-511">Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen), `OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="3160f-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="3160f-512">`Page`, bir `PageResult` örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="3160f-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="3160f-513">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="3160f-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3160f-514">`PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="3160f-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="3160f-515">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="3160f-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="3160f-516">`Customer`Özelliği `[BindProperty]` model bağlamayı kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="3160f-517">Razor Pages, varsayılan olarak yalnızca fiiller olmayan özellikleri bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="3160f-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="3160f-518">Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="3160f-519">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="3160f-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="3160f-520">Giriş sayfası (*Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="3160f-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="3160f-521">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3160f-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="3160f-522">*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="3160f-523">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="3160f-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3160f-524">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3160f-525">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="3160f-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="3160f-526">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="3160f-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="3160f-527">Etiket Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="3160f-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="3160f-528">*Pages/Edit. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="3160f-529">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3160f-530">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="3160f-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3160f-531">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="3160f-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3160f-532">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3160f-533">*Pages/Edit. cshtml. cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="3160f-534">*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="3160f-535">Sil düğmesi HTML biçiminde işlendiğinde, `formaction` için parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="3160f-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="3160f-536">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="3160f-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="3160f-537">`handler`Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="3160f-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="3160f-538">Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1` :</span><span class="sxs-lookup"><span data-stu-id="3160f-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="3160f-539">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="3160f-540">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="3160f-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="3160f-541">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="3160f-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="3160f-542">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="3160f-543">Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="3160f-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="3160f-544">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3160f-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="3160f-545">Sorgu dizesinden öğesini kabul eder `id` .</span><span class="sxs-lookup"><span data-stu-id="3160f-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="3160f-546">*Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması içeriyorsa `"{id:int?}"` , `id` Rota verilerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="3160f-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="3160f-547">İçin rota verileri, gibi `id` URI 'de belirtilmiştir `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="3160f-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="3160f-548">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="3160f-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="3160f-549">Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="3160f-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="3160f-550">Veritabanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="3160f-550">The database is updated.</span></span>
* <span data-ttu-id="3160f-551">`RedirectToPage`Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="3160f-552">Sayfa özelliklerini gerektiği gibi işaretle</span><span class="sxs-lookup"><span data-stu-id="3160f-552">Mark page properties as required</span></span>

<span data-ttu-id="3160f-553">İçindeki Özellikler `PageModel` [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:</span><span class="sxs-lookup"><span data-stu-id="3160f-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="3160f-554">Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3160f-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="3160f-555">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="3160f-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="3160f-556">`HEAD`istekleri belirli bir kaynak için üstbilgileri almanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="3160f-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="3160f-557">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="3160f-558">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="3160f-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="3160f-559">ASP.NET Core 2,1 veya sonraki bir sürümde, `OnGet` hiçbir işleyici tanımlanmazsa, Razor Pages işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="3160f-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="3160f-560">Bu davranış, içindeki [Setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3160f-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="3160f-561">Varsayılan Şablonlar `SetCompatibilityVersion` ASP.NET Core 2,1 ve 2,2 ' de çağrıyı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3160f-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="3160f-562">`SetCompatibilityVersion`Razor Pages seçeneğini etkin bir `AllowMappingHeadRequestsToGetHandler` şekilde ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="3160f-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="3160f-563">İle tüm davranışlardan çıkmak yerine `SetCompatibilityVersion` , açıkça *belirli* davranışları kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3160f-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="3160f-564">Aşağıdaki kod, isteklerin işleyiciye eşlenmesine izin vermek için ' de `HEAD` kullanılır `OnGet` :</span><span class="sxs-lookup"><span data-stu-id="3160f-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3160f-565">XSRF/CSRF ve Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3160f-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3160f-566">[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3160f-567">Antiforgery belirteci oluşturma ve doğrulama, Razor Pages otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3160f-568">Razor Pages ile düzenleri, partileri, şablonları ve etiket yardımcılarını kullanma</span><span class="sxs-lookup"><span data-stu-id="3160f-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3160f-569">Sayfalar, Razor görünüm altyapısının tüm özellikleri ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="3160f-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="3160f-570">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*, *_ViewImports. cshtml* geleneksel Razor görünümlerinde oldukları gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="3160f-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3160f-571">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="3160f-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="3160f-572">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="3160f-573">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="3160f-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3160f-574">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="3160f-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3160f-575">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="3160f-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="3160f-576">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="3160f-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="3160f-577">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3160f-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="3160f-578">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="3160f-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="3160f-579">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="3160f-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3160f-580">*Sayfalar/paylaşılan* klasördeki bir düzen, *Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3160f-581">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="3160f-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="3160f-582">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="3160f-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3160f-583">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="3160f-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3160f-584">Razor Pages, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="3160f-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3160f-585">Bir Razor sayfasından arama görüntüleme, *Sayfalar* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3160f-586">MVC denetleyicileri ve geleneksel Razor görünümleriyle kullandığınız düzenler, şablonlar ve parals işlemleri *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="3160f-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3160f-587">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3160f-588">`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3160f-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3160f-589">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="3160f-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3160f-590">`@namespace`Yönerge bir sayfada açıkça kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="3160f-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3160f-591">Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3160f-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="3160f-592">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3160f-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3160f-593">`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="3160f-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3160f-594">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="3160f-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3160f-595">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3160f-596">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="3160f-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3160f-597">*Pages/Customers/Edit. cshtml* Razor sayfasının oluşturulan ad alanı `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="3160f-598">`@namespace`*Ayrıca geleneksel Razor görünümleriyle birlikte da geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="3160f-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="3160f-599">Özgün *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="3160f-600">Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="3160f-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="3160f-601">[Razor Pages Starter projesi](#rpvs17) , istemci tarafı doğrulamayı bağlayan *sayfaları/_ValidationScriptsPartial. cshtml*'yi içerir.</span><span class="sxs-lookup"><span data-stu-id="3160f-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="3160f-602">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="3160f-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3160f-603">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="3160f-603">URL generation for Pages</span></span>

<span data-ttu-id="3160f-604">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="3160f-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="3160f-605">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="3160f-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3160f-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="3160f-606">*/Pages*</span></span>

  * <span data-ttu-id="3160f-607">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="3160f-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="3160f-608">*/Customers*</span></span>

    * <span data-ttu-id="3160f-609">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="3160f-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="3160f-610">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3160f-611">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3160f-611">*Index.cshtml*</span></span>

<span data-ttu-id="3160f-612">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="3160f-613">Dize, `/Index` önceki sayfaya erişmek IÇIN URI 'nin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="3160f-614">Dize, `/Index` *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3160f-615">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3160f-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3160f-616">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="3160f-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="3160f-617">Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="3160f-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="3160f-618">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3160f-619">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="3160f-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3160f-620">Aşağıdaki tabloda, `RedirectToPage` *sayfa/müşteri/oluşturma. cshtml*'den farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3160f-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="3160f-621">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="3160f-621">RedirectToPage(x)</span></span>| <span data-ttu-id="3160f-622">Sayfa</span><span class="sxs-lookup"><span data-stu-id="3160f-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3160f-623">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="3160f-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3160f-624">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-624">*Pages/Index*</span></span> |
| <span data-ttu-id="3160f-625">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="3160f-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3160f-626">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3160f-627">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="3160f-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3160f-628">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-628">*Pages/Index*</span></span> |
| <span data-ttu-id="3160f-629">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="3160f-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3160f-630">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="3160f-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="3160f-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="3160f-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="3160f-632">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="3160f-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="3160f-633">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="3160f-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3160f-634">Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3160f-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="3160f-635">Tüm bağlantılar hala çalışır (klasör adını içermediği için).</span><span class="sxs-lookup"><span data-stu-id="3160f-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="3160f-636">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="3160f-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="3160f-637">Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="3160f-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="3160f-638">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="3160f-638">ViewData attribute</span></span>

<span data-ttu-id="3160f-639">Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="3160f-640">' Deki denetleyicilerde veya Razor sayfa modellerinde bulunan özelliklerin `[ViewData]` değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="3160f-641">Aşağıdaki örnekte, `AboutModel` `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="3160f-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="3160f-642">`Title`Özelliği hakkında sayfasının başlığına ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3160f-642">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="3160f-643">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="3160f-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="3160f-644">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="3160f-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="3160f-645">TempData</span><span class="sxs-lookup"><span data-stu-id="3160f-645">TempData</span></span>

<span data-ttu-id="3160f-646">ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="3160f-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="3160f-647">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="3160f-647">This property stores data until it's read.</span></span> <span data-ttu-id="3160f-648">`Keep`Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3160f-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3160f-649">`TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3160f-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3160f-650">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="3160f-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3160f-651">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="3160f-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3160f-652">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="3160f-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3160f-653">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="3160f-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3160f-654">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="3160f-654">Multiple handlers per page</span></span>

<span data-ttu-id="3160f-655">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="3160f-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="3160f-656">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="3160f-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3160f-657">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3160f-658">`asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3160f-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3160f-659">`asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="3160f-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3160f-660">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="3160f-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3160f-661">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3160f-662">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="3160f-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3160f-663">Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="3160f-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3160f-664">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="3160f-665">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="3160f-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3160f-666">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="3160f-667">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="3160f-667">Custom routes</span></span>

<span data-ttu-id="3160f-668">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3160f-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="3160f-669">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="3160f-669">Specify a custom route to a page.</span></span> <span data-ttu-id="3160f-670">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="3160f-671">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-671">Append segments to a page's default route.</span></span> <span data-ttu-id="3160f-672">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="3160f-673">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3160f-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="3160f-674">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="3160f-675">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3160f-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="3160f-676">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="3160f-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="3160f-677">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3160f-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3160f-678">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="3160f-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3160f-679">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="3160f-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="3160f-680">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="3160f-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="3160f-681">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3160f-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="3160f-682">Yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="3160f-682">Configuration and settings</span></span>

<span data-ttu-id="3160f-683">Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC Oluşturucu 'da genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3160f-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="3160f-684">Şu anda `RazorPagesOptions` ' nı, sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3160f-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3160f-685">Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.</span><span class="sxs-lookup"><span data-stu-id="3160f-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="3160f-686">Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="3160f-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="3160f-687">[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="3160f-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="3160f-688">Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3160f-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="3160f-689">RazorSayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="3160f-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="3160f-690">Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="3160f-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="3160f-691">Sayfalarınızın [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) , uygulamanın [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) Razor [içerik kökünde](xref:fundamentals/index#content-root) ([Contentrootpath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) olduğunu belirtmek için addmvc ' ye WithRazorPagesAtContentRoot ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3160f-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="3160f-692">RazorSayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="3160f-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="3160f-693">Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin Razor (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="3160f-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="3160f-694">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3160f-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
