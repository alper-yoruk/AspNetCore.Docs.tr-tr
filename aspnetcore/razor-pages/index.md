---
title: RazorASP.NET Core sayfalara giriş
author: Rick-Anderson
description: RazorASP.NET Core sayfalarındaki sayfaların, MVC kullanmaktan daha kolay ve daha üretken olmasını nasıl sağladığını açıklar.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: f8cdbbffae9b291923a6d425fef5526b0ec88f61
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253195"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="1ce21-103">RazorASP.NET Core sayfalara giriş</span><span class="sxs-lookup"><span data-stu-id="1ce21-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="1ce21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="1ce21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="1ce21-105">Razor Sayfalar, denetleyici ve görünümleri kullanmaktan daha kolay ve daha üretken bir şekilde kodlama sayfasına odaklanmış senaryolar yapabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="1ce21-106">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="1ce21-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="1ce21-107">Bu belge, sayfalara giriş sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="1ce21-108">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="1ce21-109">Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="1ce21-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="1ce21-110">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="1ce21-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1ce21-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1ce21-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1ce21-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ce21-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ce21-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ce21-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1ce21-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ce21-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ce21-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ce21-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="1ce21-118">RazorSayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ce21-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ce21-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1ce21-120">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ce21-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ce21-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1ce21-122">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1ce21-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ce21-123">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1ce21-124">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="1ce21-125">Razor Sayfaları</span><span class="sxs-lookup"><span data-stu-id="1ce21-125">Razor Pages</span></span>

<span data-ttu-id="1ce21-126">Razor*Startup.cs*'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="1ce21-127">Temel bir sayfa düşünün: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="1ce21-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="1ce21-128">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="1ce21-129">Bu, farklı kılan [`@page`](xref:mvc/views/razor#page) yönergedir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1ce21-130">`@page` dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="1ce21-131">`@page` sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1ce21-132">`@page` diğer yapıların davranışını etkiler [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="1ce21-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="1ce21-133">Razor Sayfaların dosya adlarında *. cshtml* soneki vardır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="1ce21-134">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="1ce21-135">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="1ce21-136">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="1ce21-137">Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="1ce21-138">Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="1ce21-139">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="1ce21-140">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="1ce21-141">Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="1ce21-142">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="1ce21-142">File name and path</span></span>               | <span data-ttu-id="1ce21-143">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="1ce21-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1ce21-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="1ce21-145">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="1ce21-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="1ce21-146">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="1ce21-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="1ce21-147">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="1ce21-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="1ce21-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="1ce21-149">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="1ce21-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="1ce21-150">Notlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-150">Notes:</span></span>

* <span data-ttu-id="1ce21-151">Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak  arar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="1ce21-152">`Index` , URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="1ce21-153">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="1ce21-153">Write a basic form</span></span>

<span data-ttu-id="1ce21-154">Razor Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="1ce21-155">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="1ce21-156">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="1ce21-157">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="1ce21-158">Bellek içi veritabanı `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="1ce21-159">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="1ce21-160">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="1ce21-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="1ce21-161">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="1ce21-162">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="1ce21-163">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="1ce21-164">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="1ce21-165">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="1ce21-166">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-166">This separation allows:</span></span>

* <span data-ttu-id="1ce21-167">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="1ce21-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="1ce21-168">Birim testi</span><span class="sxs-lookup"><span data-stu-id="1ce21-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="1ce21-169">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi* vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="1ce21-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="1ce21-170">Herhangi bir HTTP fiili için işleyici metotları eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="1ce21-171">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-171">The most common handlers are:</span></span>

* <span data-ttu-id="1ce21-172">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="1ce21-173">Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* Razor sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="1ce21-174">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="1ce21-175">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="1ce21-176">Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="1ce21-177">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="1ce21-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="1ce21-178">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="1ce21-179">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve sayfalarla aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="1ce21-180">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="1ce21-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="1ce21-181">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="1ce21-182">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-182">Check for validation errors.</span></span>

* <span data-ttu-id="1ce21-183">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="1ce21-184">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="1ce21-185">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="1ce21-186">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="1ce21-187">Sayfalardan işlenmiş HTML */Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1ce21-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="1ce21-188">Önceki kodda, formu deftere nakletme:</span><span class="sxs-lookup"><span data-stu-id="1ce21-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="1ce21-189">Geçerli verilerle:</span><span class="sxs-lookup"><span data-stu-id="1ce21-189">With valid data:</span></span>

  * <span data-ttu-id="1ce21-190">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="1ce21-191">`RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="1ce21-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="1ce21-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="1ce21-193">Bir eylem sonucudur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-193">Is an action result.</span></span>
    * <span data-ttu-id="1ce21-194">, Veya ile `RedirectToAction` benzerdir `RedirectToRoute` (denetleyiciler ve görünümlerde kullanılır).</span><span class="sxs-lookup"><span data-stu-id="1ce21-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="1ce21-195">Sayfalar için özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-195">Is customized for pages.</span></span> <span data-ttu-id="1ce21-196">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="1ce21-197">`RedirectToPage` , [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="1ce21-198">Sunucuya geçirilen doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="1ce21-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="1ce21-199">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="1ce21-200">`Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="1ce21-201">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="1ce21-202">`PageResult` , bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="1ce21-203">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="1ce21-204">Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="1ce21-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="1ce21-205">Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="1ce21-206">Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="1ce21-207">İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="1ce21-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="1ce21-208">Veriler sunucuya **nakledilmedi.**</span><span class="sxs-lookup"><span data-stu-id="1ce21-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="1ce21-209">İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="1ce21-210">`Customer`Özelliği, [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) model bağlamasını kabul etmek için özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="1ce21-211">`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="1ce21-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="1ce21-212">Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="1ce21-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="1ce21-213">Razor Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="1ce21-214">Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="1ce21-215">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1ce21-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="1ce21-216">*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:</span><span class="sxs-lookup"><span data-stu-id="1ce21-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="1ce21-217">Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="1ce21-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) Etiket Yardımcıları kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="1ce21-219">Giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="1ce21-219">The home page</span></span>

<span data-ttu-id="1ce21-220">*Index. cshtml* giriş sayfasıdır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="1ce21-221">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1ce21-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1ce21-222">*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="1ce21-223">`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="1ce21-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="1ce21-224">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="1ce21-225">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="1ce21-226">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="1ce21-227">*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="1ce21-228">İşlenmiş HTML:</span><span class="sxs-lookup"><span data-stu-id="1ce21-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="1ce21-229">Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="1ce21-230">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="1ce21-231">, `handler` Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="1ce21-232">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="1ce21-233">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="1ce21-234">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="1ce21-235">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1ce21-236">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="1ce21-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="1ce21-237">`id`Sorgu dizesinden alır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="1ce21-238">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="1ce21-239">Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="1ce21-240"><xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="1ce21-241">Edit. cshtml dosyası</span><span class="sxs-lookup"><span data-stu-id="1ce21-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="1ce21-242">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="1ce21-243">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="1ce21-244">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1ce21-245">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1ce21-246">*Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="1ce21-247">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="1ce21-247">Validation</span></span>

<span data-ttu-id="1ce21-248">Doğrulama kuralları:</span><span class="sxs-lookup"><span data-stu-id="1ce21-248">Validation rules:</span></span>

* <span data-ttu-id="1ce21-249">Model sınıfında bildirimli olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="1ce21-250">Uygulamada her yerde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="1ce21-251"><xref:System.ComponentModel.DataAnnotations>Ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="1ce21-252">Dataaçıklamalarda, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="1ce21-253">Modeli göz önünde bulundurun `Customer` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="1ce21-254">Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:</span><span class="sxs-lookup"><span data-stu-id="1ce21-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="1ce21-255">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="1ce21-255">The preceding code:</span></span>

* <span data-ttu-id="1ce21-256">JQuery ve jQuery doğrulama betikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="1ce21-257">`<div />`' İ `<span />` etkinleştirmek için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="1ce21-258">İstemci tarafı doğrulama.</span><span class="sxs-lookup"><span data-stu-id="1ce21-258">Client-side validation.</span></span>
  * <span data-ttu-id="1ce21-259">Doğrulama hatası işleme.</span><span class="sxs-lookup"><span data-stu-id="1ce21-259">Validation error rendering.</span></span>

* <span data-ttu-id="1ce21-260">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1ce21-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="1ce21-261">Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="1ce21-262">formunda.</span><span class="sxs-lookup"><span data-stu-id="1ce21-262">on the form.</span></span> <span data-ttu-id="1ce21-263">İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="1ce21-264">`[StringLength(10)]`Özniteliği `data-val-length-max="10"` işlenmiş html üzerinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="1ce21-265">`data-val-length-max` tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="1ce21-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="1ce21-266">Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:</span><span class="sxs-lookup"><span data-stu-id="1ce21-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="1ce21-267">, Adı 10 ' dan daha uzun.</span><span class="sxs-lookup"><span data-stu-id="1ce21-267">With the name longer than 10.</span></span>
* <span data-ttu-id="1ce21-268">"Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="1ce21-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="1ce21-269">hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-269">is returned.</span></span>

<span data-ttu-id="1ce21-270">Aşağıdaki modeli göz önünde bulundurun `Movie` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="1ce21-271">Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="1ce21-272">`Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="1ce21-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="1ce21-273">`RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="1ce21-274">Yukarıdaki kodda, "tarz":</span><span class="sxs-lookup"><span data-stu-id="1ce21-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="1ce21-275">Yalnızca harfler kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-275">Must only use letters.</span></span>
  * <span data-ttu-id="1ce21-276">İlk harfin büyük harfle olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="1ce21-277">Boşluk, sayı ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="1ce21-278">`RegularExpression`"Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="1ce21-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="1ce21-279">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="1ce21-280">Sonraki boşlukların içindeki özel karakter ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="1ce21-281">"PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="1ce21-282">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="1ce21-283">`StringLength`Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="1ce21-284">Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="1ce21-285">Model için Oluştur sayfasında, `Movie` geçersiz değerlere sahip hatalar görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="1ce21-287">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="1ce21-287">For more information, see:</span></span>

* [<span data-ttu-id="1ce21-288">Film uygulamasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="1ce21-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="1ce21-289">[ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="1ce21-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="1ce21-290">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="1ce21-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="1ce21-291">`HEAD` istekler belirli bir kaynak için üstbilgileri almaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="1ce21-292">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="1ce21-293">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1ce21-294">Razor Bir `OnGet` işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="1ce21-295">XSRF/CSRF ve Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="1ce21-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="1ce21-296">Razor Sayfalar, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="1ce21-297">[Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="1ce21-298">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="1ce21-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="1ce21-299">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="1ce21-300">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml* ve *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="1ce21-301">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="1ce21-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="1ce21-302">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="1ce21-303">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="1ce21-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="1ce21-304">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="1ce21-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="1ce21-305">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="1ce21-306">RazorSayfanın içeriği `@RenderBody()` olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="1ce21-307">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="1ce21-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="1ce21-308">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1ce21-309">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="1ce21-310">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="1ce21-311">*Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="1ce21-312">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="1ce21-313">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="1ce21-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="1ce21-314">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="1ce21-315">Razor Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="1ce21-316">Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="1ce21-317">MVC denetleyicileri ve geleneksel görünümler ile kullanılan düzenler, şablonlar ve partilar Razor *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="1ce21-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="1ce21-318">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="1ce21-319">`@namespace` , Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="1ce21-320">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="1ce21-321">`@namespace`Bir sayfada ayarlanan yönerge:</span><span class="sxs-lookup"><span data-stu-id="1ce21-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="1ce21-322">`@namespace`Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="1ce21-323">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="1ce21-324">`@namespace`Yönerge *_ViewImports. cshtml* içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="1ce21-325">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="1ce21-326">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="1ce21-327">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="1ce21-328">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="1ce21-329">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*</span><span class="sxs-lookup"><span data-stu-id="1ce21-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="1ce21-330">*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="1ce21-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="1ce21-331">Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="1ce21-332">Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="1ce21-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="1ce21-333">Düzen dosyası JavaScript dosyalarını içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="1ce21-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="1ce21-334">[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="1ce21-335">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="1ce21-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="1ce21-336">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ce21-336">URL generation for Pages</span></span>

<span data-ttu-id="1ce21-337">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="1ce21-338">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="1ce21-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="1ce21-339">*/Pages*</span></span>

  * <span data-ttu-id="1ce21-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="1ce21-341">*Gizlilik. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="1ce21-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="1ce21-342">*/Customers*</span></span>

    * <span data-ttu-id="1ce21-343">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="1ce21-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="1ce21-344">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="1ce21-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-345">*Index.cshtml*</span></span>

<span data-ttu-id="1ce21-346">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="1ce21-347">Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="1ce21-348">*Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="1ce21-349">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="1ce21-350">Mutlak sayfa adı, `/Index` *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="1ce21-351">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="1ce21-352">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="1ce21-353">Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="1ce21-354">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="1ce21-355">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="1ce21-356">Aşağıdaki tabloda, `RedirectToPage` *sayfalarda/müşteriler/Create. cshtml*'de farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="1ce21-357">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="1ce21-357">RedirectToPage(x)</span></span>| <span data-ttu-id="1ce21-358">Sayfa</span><span class="sxs-lookup"><span data-stu-id="1ce21-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1ce21-359">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="1ce21-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="1ce21-360">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-360">*Pages/Index*</span></span> |
| <span data-ttu-id="1ce21-361">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="1ce21-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="1ce21-362">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="1ce21-363">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="1ce21-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="1ce21-364">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-364">*Pages/Index*</span></span> |
| <span data-ttu-id="1ce21-365">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="1ce21-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="1ce21-366">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="1ce21-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="1ce21-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="1ce21-368">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="1ce21-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="1ce21-369">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="1ce21-370">Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="1ce21-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="1ce21-371">Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="1ce21-372">Klasör adını içermediği için bağlantılar kopuk değildir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="1ce21-373">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="1ce21-374">Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="1ce21-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="1ce21-375">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="1ce21-375">ViewData attribute</span></span>

<span data-ttu-id="1ce21-376">Veri, ile bir sayfaya geçirilebilir <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="1ce21-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="1ce21-377">Özniteliği olan Özellikler `[ViewData]` , değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="1ce21-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="1ce21-378">Aşağıdaki örnekte, `AboutModel` `[ViewData]` özniteliği `Title` özelliğine uygular:</span><span class="sxs-lookup"><span data-stu-id="1ce21-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="1ce21-379">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="1ce21-380">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="1ce21-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="1ce21-381">TempData</span><span class="sxs-lookup"><span data-stu-id="1ce21-381">TempData</span></span>

<span data-ttu-id="1ce21-382">ASP.NET Core, öğesini kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="1ce21-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="1ce21-383">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-383">This property stores data until it's read.</span></span> <span data-ttu-id="1ce21-384"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="1ce21-385">`TempData` , bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="1ce21-386">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="1ce21-387">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="1ce21-388">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="1ce21-389">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="1ce21-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="1ce21-390">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="1ce21-390">Multiple handlers per page</span></span>

<span data-ttu-id="1ce21-391">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="1ce21-392">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="1ce21-393">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="1ce21-394">`asp-page-handler` bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="1ce21-395">`asp-page` örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="1ce21-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="1ce21-396">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="1ce21-397">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini* kullanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="1ce21-398">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="1ce21-399">Yukarıdaki örnekte, Page metotları OnPost **Joinlist** Async ve onpost **Joinlıstuc** Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="1ce21-400">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="1ce21-401">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="1ce21-402">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="1ce21-403">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="1ce21-403">Custom routes</span></span>

<span data-ttu-id="1ce21-404">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1ce21-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="1ce21-405">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-405">Specify a custom route to a page.</span></span> <span data-ttu-id="1ce21-406">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="1ce21-407">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-407">Append segments to a page's default route.</span></span> <span data-ttu-id="1ce21-408">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="1ce21-409">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="1ce21-410">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="1ce21-411">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="1ce21-412">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="1ce21-413">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="1ce21-414">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="1ce21-415">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="1ce21-416">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="1ce21-417">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="1ce21-418">Gelişmiş yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="1ce21-418">Advanced configuration and settings</span></span>

<span data-ttu-id="1ce21-419">Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="1ce21-420">Gelişmiş seçenekleri yapılandırmak için, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> yapılandıran aşırı yüklemeyi kullanın <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="1ce21-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="1ce21-421">Sayfalar için <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="1ce21-422">Kurallar hakkında daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="1ce21-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="1ce21-423">Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="1ce21-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="1ce21-424">RazorSayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="1ce21-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="1ce21-425">Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="1ce21-426"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> Razor Sayfalarınızın, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) () olduğunu belirtmek için ekleyin <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> :</span><span class="sxs-lookup"><span data-stu-id="1ce21-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="1ce21-427">RazorSayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="1ce21-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="1ce21-428"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>Bu Razor sayfaların, uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="1ce21-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1ce21-429">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1ce21-429">Additional resources</span></span>

* <span data-ttu-id="1ce21-430">Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="1ce21-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="1ce21-431">Özniteliği ve Razor sayfaları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="1ce21-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="1ce21-432">Örnek kodu indirme veya görüntüleme</span><span class="sxs-lookup"><span data-stu-id="1ce21-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* [<span data-ttu-id="1ce21-433">Razor ASP.NET Core için sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="1ce21-433">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1ce21-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ce21-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ce21-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ce21-436">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="1ce21-437">RazorSayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ce21-437">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1ce21-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1ce21-439">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-439">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1ce21-440">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ce21-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1ce21-441">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1ce21-441">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="1ce21-442">Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.</span><span class="sxs-lookup"><span data-stu-id="1ce21-442">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1ce21-443">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ce21-443">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1ce21-444">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1ce21-444">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="1ce21-445">Razor Sayfaları</span><span class="sxs-lookup"><span data-stu-id="1ce21-445">Razor Pages</span></span>

<span data-ttu-id="1ce21-446">Razor*Startup.cs*'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-446">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="1ce21-447">Temel bir sayfa düşünün: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="1ce21-447">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="1ce21-448">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-448">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="1ce21-449">Bu, farklı kılan `@page` yönergedir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-449">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="1ce21-450">`@page` dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-450">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="1ce21-451">`@page` sayfadaki ilk yönerge olmalıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-451">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1ce21-452">`@page` diğer yapıların davranışını etkiler Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-452">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="1ce21-453">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-453">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="1ce21-454">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-454">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="1ce21-455">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-455">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="1ce21-456">Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-456">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="1ce21-457">Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-457">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="1ce21-458">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-458">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="1ce21-459">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-459">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="1ce21-460">Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-460">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="1ce21-461">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="1ce21-461">File name and path</span></span>               | <span data-ttu-id="1ce21-462">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="1ce21-462">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1ce21-463">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-463">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="1ce21-464">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="1ce21-464">`/` or `/Index`</span></span> |
| <span data-ttu-id="1ce21-465">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="1ce21-465">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="1ce21-466">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="1ce21-466">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="1ce21-467">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-467">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="1ce21-468">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="1ce21-468">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="1ce21-469">Notlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-469">Notes:</span></span>

* <span data-ttu-id="1ce21-470">Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak  arar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-470">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="1ce21-471">`Index` , URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-471">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="1ce21-472">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="1ce21-472">Write a basic form</span></span>

<span data-ttu-id="1ce21-473">Razor Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-473">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="1ce21-474">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-474">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="1ce21-475">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-475">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="1ce21-476">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-476">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="1ce21-477">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-477">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="1ce21-478">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="1ce21-478">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="1ce21-479">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-479">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="1ce21-480">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-480">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="1ce21-481">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-481">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="1ce21-482">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-482">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="1ce21-483">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-483">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="1ce21-484">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-484">This separation allows:</span></span>

* <span data-ttu-id="1ce21-485">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="1ce21-485">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="1ce21-486">Sayfaların [birim testi](xref:test/razor-pages-tests) .</span><span class="sxs-lookup"><span data-stu-id="1ce21-486">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="1ce21-487">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi* vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="1ce21-487">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="1ce21-488">Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-488">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="1ce21-489">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-489">The most common handlers are:</span></span>

* <span data-ttu-id="1ce21-490">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-490">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="1ce21-491">[OnGet](#OnGet) örneği.</span><span class="sxs-lookup"><span data-stu-id="1ce21-491">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="1ce21-492">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-492">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="1ce21-493">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-493">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="1ce21-494">Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-494">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="1ce21-495">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="1ce21-495">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="1ce21-496">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-496">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="1ce21-497">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-497">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="1ce21-498">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="1ce21-498">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="1ce21-499">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-499">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="1ce21-500">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-500">Check for validation errors.</span></span>

* <span data-ttu-id="1ce21-501">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-501">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="1ce21-502">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-502">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="1ce21-503">İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-503">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="1ce21-504">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-504">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="1ce21-505">Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için yardımcı yöntemini çağırır `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-505">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="1ce21-506">`RedirectToPage` , `RedirectToAction` veya ' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur `RedirectToRoute` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-506">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="1ce21-507">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-507">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="1ce21-508">`RedirectToPage` , [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-508">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="1ce21-509">Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen), `OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-509">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="1ce21-510">`Page`, bir `PageResult` örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-510">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="1ce21-511">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-511">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="1ce21-512">`PageResult` , bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-512">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="1ce21-513">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-513">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="1ce21-514">`Customer`Özelliği `[BindProperty]` model bağlamayı kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-514">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="1ce21-515">Razor Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-515">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="1ce21-516">Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-516">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="1ce21-517">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1ce21-517">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="1ce21-518">Giriş sayfası (*Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="1ce21-518">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="1ce21-519">İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1ce21-519">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="1ce21-520">*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-520">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="1ce21-521">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="1ce21-521">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="1ce21-522">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-522">The link contains route data with the contact ID.</span></span> <span data-ttu-id="1ce21-523">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="1ce21-523">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="1ce21-524">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-524">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="1ce21-525">Etiket Yardımcıları tarafından etkinleştirilir `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="1ce21-525">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="1ce21-526">*Pages/Edit. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-526">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="1ce21-527">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-527">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="1ce21-528">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-528">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="1ce21-529">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="1ce21-529">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1ce21-530">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-530">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1ce21-531">*Pages/Edit. cshtml. cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-531">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="1ce21-532">*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-532">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="1ce21-533">Sil düğmesi HTML biçiminde işlendiğinde, `formaction` için parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-533">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="1ce21-534">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-534">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="1ce21-535">`handler`Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-535">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="1ce21-536">Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-536">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="1ce21-537">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-537">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="1ce21-538">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-538">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="1ce21-539">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-539">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="1ce21-540">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-540">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="1ce21-541">Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-541">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="1ce21-542">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="1ce21-542">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="1ce21-543">Sorgu dizesinden öğesini kabul eder `id` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-543">Accepts the `id` from the query string.</span></span> <span data-ttu-id="1ce21-544">*Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması içeriyorsa `"{id:int?}"` , `id` Rota verilerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-544">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="1ce21-545">İçin rota verileri, gibi `id` URI 'de belirtilmiştir `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-545">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="1ce21-546">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-546">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="1ce21-547">Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-547">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="1ce21-548">Veritabanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="1ce21-548">The database is updated.</span></span>
* <span data-ttu-id="1ce21-549">`RedirectToPage`Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-549">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="1ce21-550">Sayfa özelliklerini gerektiği gibi işaretle</span><span class="sxs-lookup"><span data-stu-id="1ce21-550">Mark page properties as required</span></span>

<span data-ttu-id="1ce21-551">İçindeki Özellikler `PageModel` [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-551">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="1ce21-552">Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="1ce21-552">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="1ce21-553">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="1ce21-553">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="1ce21-554">`HEAD` istekleri belirli bir kaynak için üstbilgileri almanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-554">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="1ce21-555">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-555">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="1ce21-556">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-556">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="1ce21-557">ASP.NET Core 2,1 veya üzeri sürümlerde, Razor `OnGet` hiçbir işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-557">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="1ce21-558">Bu davranış, içindeki [Setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-558">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="1ce21-559">Varsayılan Şablonlar `SetCompatibilityVersion` ASP.NET Core 2,1 ve 2,2 ' de çağrıyı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-559">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="1ce21-560">`SetCompatibilityVersion`Razorsayfa seçeneğini etkin bir `AllowMappingHeadRequestsToGetHandler` şekilde ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-560">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="1ce21-561">İle tüm davranışlardan çıkmak yerine `SetCompatibilityVersion` , açıkça *belirli* davranışları kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-561">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="1ce21-562">Aşağıdaki kod, isteklerin işleyiciye eşlenmesine izin vermek için ' de `HEAD` kullanılır `OnGet` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-562">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="1ce21-563">XSRF/CSRF ve Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="1ce21-563">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="1ce21-564">[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-564">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="1ce21-565">Antiforgery belirteci oluşturma ve doğrulama, sayfalara otomatik olarak eklenir Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-565">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="1ce21-566">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="1ce21-566">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="1ce21-567">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-567">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="1ce21-568">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*, *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .</span><span class="sxs-lookup"><span data-stu-id="1ce21-568">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="1ce21-569">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="1ce21-569">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="1ce21-570">*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-570">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="1ce21-571">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="1ce21-571">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="1ce21-572">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="1ce21-572">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="1ce21-573">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-573">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="1ce21-574">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="1ce21-574">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="1ce21-575">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-575">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1ce21-576">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-576">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="1ce21-577">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-577">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="1ce21-578">*Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-578">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="1ce21-579">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-579">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="1ce21-580">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="1ce21-580">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="1ce21-581">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-581">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="1ce21-582">Razor Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-582">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="1ce21-583">Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-583">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="1ce21-584">MVC denetleyicileri ve geleneksel görünümler ile kullanmakta olduğunuz düzenler, şablonlar ve parals Razor *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="1ce21-584">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="1ce21-585">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-585">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="1ce21-586">`@namespace` , Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-586">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="1ce21-587">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-587">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="1ce21-588">`@namespace`Yönerge bir sayfada açıkça kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="1ce21-588">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="1ce21-589">Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-589">The directive sets the namespace for the page.</span></span> <span data-ttu-id="1ce21-590">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1ce21-590">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="1ce21-591">`@namespace`Yönerge *_ViewImports. cshtml* içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-591">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="1ce21-592">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-592">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="1ce21-593">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-593">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="1ce21-594">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1ce21-594">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="1ce21-595">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-595">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="1ce21-596">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*</span><span class="sxs-lookup"><span data-stu-id="1ce21-596">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="1ce21-597">Özgün *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-597">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="1ce21-598">Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="1ce21-598">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="1ce21-599">[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-599">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="1ce21-600">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="1ce21-600">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="1ce21-601">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="1ce21-601">URL generation for Pages</span></span>

<span data-ttu-id="1ce21-602">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-602">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="1ce21-603">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-603">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="1ce21-604">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="1ce21-604">*/Pages*</span></span>

  * <span data-ttu-id="1ce21-605">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-605">*Index.cshtml*</span></span>
  * <span data-ttu-id="1ce21-606">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="1ce21-606">*/Customers*</span></span>

    * <span data-ttu-id="1ce21-607">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="1ce21-607">*Create.cshtml*</span></span>
    * <span data-ttu-id="1ce21-608">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-608">*Edit.cshtml*</span></span>
    * <span data-ttu-id="1ce21-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1ce21-609">*Index.cshtml*</span></span>

<span data-ttu-id="1ce21-610">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-610">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="1ce21-611">Dize, `/Index` önceki sayfaya erişmek IÇIN URI 'nin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-611">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="1ce21-612">Dize, `/Index` *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-612">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="1ce21-613">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-613">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="1ce21-614">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-614">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="1ce21-615">Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-615">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="1ce21-616">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-616">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="1ce21-617">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="1ce21-617">URL generation for pages supports relative names.</span></span> <span data-ttu-id="1ce21-618">Aşağıdaki tabloda, `RedirectToPage` *sayfa/müşteri/oluşturma. cshtml*'den farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1ce21-618">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="1ce21-619">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="1ce21-619">RedirectToPage(x)</span></span>| <span data-ttu-id="1ce21-620">Sayfa</span><span class="sxs-lookup"><span data-stu-id="1ce21-620">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1ce21-621">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="1ce21-621">RedirectToPage("/Index")</span></span> | <span data-ttu-id="1ce21-622">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-622">*Pages/Index*</span></span> |
| <span data-ttu-id="1ce21-623">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="1ce21-623">RedirectToPage("./Index");</span></span> | <span data-ttu-id="1ce21-624">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-624">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="1ce21-625">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="1ce21-625">RedirectToPage("../Index")</span></span> | <span data-ttu-id="1ce21-626">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-626">*Pages/Index*</span></span> |
| <span data-ttu-id="1ce21-627">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="1ce21-627">RedirectToPage("Index")</span></span>  | <span data-ttu-id="1ce21-628">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="1ce21-628">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="1ce21-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="1ce21-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="1ce21-630">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="1ce21-630">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="1ce21-631">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-631">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="1ce21-632">Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-632">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="1ce21-633">Tüm bağlantılar hala çalışır (klasör adını içermediği için).</span><span class="sxs-lookup"><span data-stu-id="1ce21-633">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="1ce21-634">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-634">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="1ce21-635">Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="1ce21-635">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="1ce21-636">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="1ce21-636">ViewData attribute</span></span>

<span data-ttu-id="1ce21-637">Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-637">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="1ce21-638">' Deki denetleyicilerde veya Razor sayfa modellerinde bulunan özelliklerin `[ViewData]` değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-638">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="1ce21-639">Aşağıdaki örnekte, `AboutModel` `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-639">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="1ce21-640">`Title`Özelliği hakkında sayfasının başlığına ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="1ce21-640">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="1ce21-641">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="1ce21-641">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="1ce21-642">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="1ce21-642">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="1ce21-643">TempData</span><span class="sxs-lookup"><span data-stu-id="1ce21-643">TempData</span></span>

<span data-ttu-id="1ce21-644">ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-644">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="1ce21-645">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="1ce21-645">This property stores data until it's read.</span></span> <span data-ttu-id="1ce21-646">`Keep`Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-646">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="1ce21-647">`TempData` , bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-647">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="1ce21-648">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-648">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="1ce21-649">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-649">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="1ce21-650">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-650">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="1ce21-651">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="1ce21-651">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="1ce21-652">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="1ce21-652">Multiple handlers per page</span></span>

<span data-ttu-id="1ce21-653">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="1ce21-653">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="1ce21-654">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-654">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="1ce21-655">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-655">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="1ce21-656">`asp-page-handler` bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1ce21-656">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="1ce21-657">`asp-page` örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="1ce21-657">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="1ce21-658">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="1ce21-658">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="1ce21-659">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini* kullanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-659">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="1ce21-660">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-660">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="1ce21-661">Yukarıdaki örnekte, Page metotları OnPost **Joinlist** Async ve onpost **Joinlıstuc** Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-661">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="1ce21-662">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-662">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="1ce21-663">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-663">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="1ce21-664">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-664">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="1ce21-665">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="1ce21-665">Custom routes</span></span>

<span data-ttu-id="1ce21-666">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1ce21-666">Use the `@page` directive to:</span></span>

* <span data-ttu-id="1ce21-667">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-667">Specify a custom route to a page.</span></span> <span data-ttu-id="1ce21-668">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-668">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="1ce21-669">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-669">Append segments to a page's default route.</span></span> <span data-ttu-id="1ce21-670">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-670">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="1ce21-671">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-671">Append parameters to a page's default route.</span></span> <span data-ttu-id="1ce21-672">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-672">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="1ce21-673">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-673">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="1ce21-674">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-674">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="1ce21-675">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1ce21-675">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="1ce21-676">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-676">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="1ce21-677">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-677">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="1ce21-678">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="1ce21-678">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="1ce21-679">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1ce21-679">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="1ce21-680">Yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="1ce21-680">Configuration and settings</span></span>

<span data-ttu-id="1ce21-681">Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC Oluşturucu 'da genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1ce21-681">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="1ce21-682">Şu anda `RazorPagesOptions` ' nı, sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-682">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="1ce21-683">Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.</span><span class="sxs-lookup"><span data-stu-id="1ce21-683">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="1ce21-684">Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="1ce21-684">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="1ce21-685">[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="1ce21-685">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="1ce21-686">Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="1ce21-686">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="1ce21-687">RazorSayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="1ce21-687">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="1ce21-688">Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="1ce21-688">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="1ce21-689">Sayfalarınızın uygulamanın içerik kökünde (contentrootpath) olduğunu belirtmek için [ Razor pagesatcontentroot Ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) [addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ' ye ekleyin Razor : [](xref:fundamentals/index#content-root) [](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)</span><span class="sxs-lookup"><span data-stu-id="1ce21-689">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="1ce21-690">RazorSayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="1ce21-690">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="1ce21-691">Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye [ Razor pagesroot ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin Razor (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="1ce21-691">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="1ce21-692">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1ce21-692">Additional resources</span></span>

* [<span data-ttu-id="1ce21-693">Özniteliği ve Razor sayfaları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="1ce21-693">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* [<span data-ttu-id="1ce21-694">Razor ASP.NET Core için sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="1ce21-694">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
