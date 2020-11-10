---
title: :::no-loc(Razor):::ASP.NET Core sayfalara giriş
author: Rick-Anderson
description: :::no-loc(Razor):::ASP.NET Core sayfalarındaki sayfaların, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/index
ms.openlocfilehash: 89e06d00e9312a428c4e164b0dc60527fe12d904
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430880"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="5bcf9-103">:::no-loc(Razor):::ASP.NET Core sayfalara giriş</span><span class="sxs-lookup"><span data-stu-id="5bcf9-103">Introduction to :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bcf9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="5bcf9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="5bcf9-105">:::no-loc(Razor)::: Sayfalar, denetleyici ve görünümleri kullanmaktan daha kolay ve daha üretken bir şekilde kodlama sayfasına odaklanmış senaryolar yapabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-105">:::no-loc(Razor)::: Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="5bcf9-106">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="5bcf9-107">Bu belge, sayfalara giriş sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-107">This document provides an introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="5bcf9-108">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="5bcf9-109">Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-109">If you find some of the sections too advanced, see [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="5bcf9-110">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5bcf9-111">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bcf9-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bcf9-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bcf9-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5bcf9-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="5bcf9-115">:::no-loc(Razor):::Sayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-115">Create a :::no-loc(Razor)::: Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bcf9-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5bcf9-117">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-117">See [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a :::no-loc(Razor)::: Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bcf9-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bcf9-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5bcf9-119">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5bcf9-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5bcf9-121">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-121">See [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a :::no-loc(Razor)::: Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="5bcf9-122">:::no-loc(Razor)::: Sayfaları</span><span class="sxs-lookup"><span data-stu-id="5bcf9-122">:::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-123">:::no-loc(Razor):::*Startup.cs* 'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-123">:::no-loc(Razor)::: Pages is enabled in *Startup.cs* :</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="5bcf9-124">Temel bir sayfa düşünün: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5bcf9-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-125">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ :::no-loc(Razor)::: görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-125">The preceding code looks a lot like a [:::no-loc(Razor)::: view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5bcf9-126">Bu, farklı kılan [`@page`](xref:mvc/views/razor#page) yönergedir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="5bcf9-127">`@page` dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5bcf9-128">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-128">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="5bcf9-129">`@page` diğer yapıların davranışını etkiler [:::no-loc(Razor):::](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-129">`@page` affects the behavior of other [:::no-loc(Razor):::](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="5bcf9-130">:::no-loc(Razor)::: Sayfaların dosya adlarında *. cshtml* soneki vardır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-130">:::no-loc(Razor)::: Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="5bcf9-131">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5bcf9-132">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5bcf9-133">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5bcf9-134">Kural gereği, `PageModel` sınıf dosyası :::no-loc(Razor)::: *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-134">By convention, the `PageModel` class file has the same name as the :::no-loc(Razor)::: Page file with *.cs* appended.</span></span> <span data-ttu-id="5bcf9-135">Örneğin, önceki :::no-loc(Razor)::: sayfa *Pages/Index2. cshtml* ' dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-135">For example, the previous :::no-loc(Razor)::: Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5bcf9-136">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5bcf9-137">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5bcf9-138">Aşağıdaki tabloda, bir :::no-loc(Razor)::: sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-138">The following table shows a :::no-loc(Razor)::: Page path and the matching URL:</span></span>

| <span data-ttu-id="5bcf9-139">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="5bcf9-139">File name and path</span></span>               | <span data-ttu-id="5bcf9-140">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="5bcf9-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5bcf9-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5bcf9-142">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="5bcf9-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="5bcf9-143">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5bcf9-144">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5bcf9-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5bcf9-146">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5bcf9-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5bcf9-147">Notlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-147">Notes:</span></span>

* <span data-ttu-id="5bcf9-148">Çalışma zamanı sayfalar :::no-loc(Razor)::: klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-148">The runtime looks for :::no-loc(Razor)::: Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5bcf9-149">`Index` , URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5bcf9-150">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-150">Write a basic form</span></span>

<span data-ttu-id="5bcf9-151">:::no-loc(Razor)::: Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-151">:::no-loc(Razor)::: Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5bcf9-152">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a :::no-loc(Razor)::: Page class.</span></span> <span data-ttu-id="5bcf9-153">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5bcf9-154">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/:::no-loc(Razor):::PagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/:::no-loc(Razor):::PagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="5bcf9-155">Bellek içi veritabanı `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="5bcf9-156">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Models/Customer.cs)]

<span data-ttu-id="5bcf9-157">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="5bcf9-158">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5bcf9-159">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5bcf9-160">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5bcf9-161">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5bcf9-162">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5bcf9-163">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-163">This separation allows:</span></span>

* <span data-ttu-id="5bcf9-164">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="5bcf9-165">Birim testi</span><span class="sxs-lookup"><span data-stu-id="5bcf9-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="5bcf9-166">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi* vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-166">The page has an `OnPostAsync` *handler method* , which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5bcf9-167">Herhangi bir HTTP fiili için işleyici metotları eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="5bcf9-168">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-168">The most common handlers are:</span></span>

* <span data-ttu-id="5bcf9-169">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5bcf9-170">Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* :::no-loc(Razor)::: sayfasını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* :::no-loc(Razor)::: Page.</span></span>
* <span data-ttu-id="5bcf9-171">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5bcf9-172">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5bcf9-173">Yukarıdaki kod, sayfalar için tipik bir davranıştır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-173">The preceding code is typical for :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="5bcf9-174">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5bcf9-175">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5bcf9-176">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve sayfalarla aynı şekilde çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and :::no-loc(Razor)::: Pages.</span></span> 

<span data-ttu-id="5bcf9-177">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5bcf9-178">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5bcf9-179">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-179">Check for validation errors.</span></span>

* <span data-ttu-id="5bcf9-180">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5bcf9-181">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5bcf9-182">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5bcf9-183">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5bcf9-184">Sayfalardan işlenmiş HTML */Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-184">The rendered HTML from *Pages/Create.cshtml* :</span></span>

[!code-html[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="5bcf9-185">Önceki kodda, formu deftere nakletme:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="5bcf9-186">Geçerli verilerle:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-186">With valid data:</span></span>

  * <span data-ttu-id="5bcf9-187">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="5bcf9-188">`RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="5bcf9-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="5bcf9-190">Bir eylem sonucudur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-190">Is an action result.</span></span>
    * <span data-ttu-id="5bcf9-191">, Veya ile `RedirectToAction` benzerdir `RedirectToRoute` (denetleyiciler ve görünümlerde kullanılır).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="5bcf9-192">Sayfalar için özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-192">Is customized for pages.</span></span> <span data-ttu-id="5bcf9-193">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5bcf9-194">`RedirectToPage` , [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="5bcf9-195">Sunucuya geçirilen doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="5bcf9-196">`OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Page*> yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="5bcf9-197">`Page`, bir <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageResult> örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageResult>.</span></span> <span data-ttu-id="5bcf9-198">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5bcf9-199">`PageResult` , bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5bcf9-200">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="5bcf9-201">Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="5bcf9-202">Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="5bcf9-203">Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="5bcf9-204">İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="5bcf9-205">Veriler sunucuya **nakledilmedi.**</span><span class="sxs-lookup"><span data-stu-id="5bcf9-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="5bcf9-206">İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="5bcf9-207">`Customer`Özelliği, [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) model bağlamasını kabul etmek için özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="5bcf9-208">`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="5bcf9-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="5bcf9-209">Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="5bcf9-210">:::no-loc(Razor)::: Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-210">:::no-loc(Razor)::: Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5bcf9-211">Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="5bcf9-212">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5bcf9-213">*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="5bcf9-214">Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="5bcf9-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) Etiket Yardımcıları kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="5bcf9-216">Giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="5bcf9-216">The home page</span></span>

<span data-ttu-id="5bcf9-217">*Index. cshtml* giriş sayfasıdır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="5bcf9-218">İlişkili `PageModel` Sınıf ( *Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-218">The associated `PageModel` class ( *Index.cshtml.cs* ):</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5bcf9-219">*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="5bcf9-220">`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5bcf9-221">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5bcf9-222">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5bcf9-223">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span>

<span data-ttu-id="5bcf9-224">*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="5bcf9-225">İşlenmiş HTML:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5bcf9-226">Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="5bcf9-227">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5bcf9-228">, `handler` Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5bcf9-229">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5bcf9-230">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5bcf9-231">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5bcf9-232">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="5bcf9-233">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5bcf9-234">`id`Sorgu dizesinden alır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="5bcf9-235">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5bcf9-236">Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="5bcf9-237"><xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.RedirectToPage*>Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-237">Calls <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="5bcf9-238">Edit. cshtml dosyası</span><span class="sxs-lookup"><span data-stu-id="5bcf9-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-239">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5bcf9-240">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5bcf9-241">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5bcf9-242">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5bcf9-243">*Edit.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="5bcf9-244">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="5bcf9-244">Validation</span></span>

<span data-ttu-id="5bcf9-245">Doğrulama kuralları:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-245">Validation rules:</span></span>

* <span data-ttu-id="5bcf9-246">Model sınıfında bildirimli olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="5bcf9-247">Uygulamada her yerde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="5bcf9-248"><xref:System.ComponentModel.DataAnnotations>Ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="5bcf9-249">Dataaçıklamalarda, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="5bcf9-250">Modeli göz önünde bulundurun `Customer` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Models/Customer.cs)]

<span data-ttu-id="5bcf9-251">Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="5bcf9-252">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-252">The preceding code:</span></span>

* <span data-ttu-id="5bcf9-253">JQuery ve jQuery doğrulama betikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="5bcf9-254">`<div />`' İ `<span />` etkinleştirmek için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="5bcf9-255">İstemci tarafı doğrulama.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-255">Client-side validation.</span></span>
  * <span data-ttu-id="5bcf9-256">Doğrulama hatası işleme.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-256">Validation error rendering.</span></span>

* <span data-ttu-id="5bcf9-257">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="5bcf9-258">Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="5bcf9-259">formunda.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-259">on the form.</span></span> <span data-ttu-id="5bcf9-260">İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="5bcf9-261">`[StringLength(10)]`Özniteliği `data-val-length-max="10"` işlenmiş html üzerinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="5bcf9-262">`data-val-length-max` tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="5bcf9-263">Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="5bcf9-264">, Adı 10 ' dan daha uzun.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-264">With the name longer than 10.</span></span>
* <span data-ttu-id="5bcf9-265">"Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="5bcf9-266">hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-266">is returned.</span></span>

<span data-ttu-id="5bcf9-267">Aşağıdaki modeli göz önünde bulundurun `Movie` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="5bcf9-268">Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="5bcf9-269">`Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="5bcf9-270">`RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="5bcf9-271">Yukarıdaki kodda, "tarz":</span><span class="sxs-lookup"><span data-stu-id="5bcf9-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="5bcf9-272">Yalnızca harfler kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-272">Must only use letters.</span></span>
  * <span data-ttu-id="5bcf9-273">İlk harfin büyük harfle olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="5bcf9-274">Boşluk, sayı ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="5bcf9-275">`RegularExpression`"Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="5bcf9-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="5bcf9-276">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="5bcf9-277">Sonraki boşlukların içindeki özel karakter ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="5bcf9-278">"PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="5bcf9-279">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="5bcf9-280">`StringLength`Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="5bcf9-281">Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="5bcf9-282">Model için Oluştur sayfasında, `Movie` geçersiz değerlere sahip hatalar görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="5bcf9-284">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-284">For more information, see:</span></span>

* [<span data-ttu-id="5bcf9-285">Film uygulamasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="5bcf9-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="5bcf9-286">[ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5bcf9-287">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="5bcf9-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5bcf9-288">`HEAD` istekler belirli bir kaynak için üstbilgileri almaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="5bcf9-289">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5bcf9-290">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="5bcf9-291">:::no-loc(Razor)::: Bir `OnGet` işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-291">:::no-loc(Razor)::: Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="5bcf9-292">XSRF/CSRF ve :::no-loc(Razor)::: Sayfalar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-292">XSRF/CSRF and :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-293">:::no-loc(Razor)::: Sayfalar, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-293">:::no-loc(Razor)::: Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5bcf9-294">[Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="5bcf9-295">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="5bcf9-295">Using Layouts, partials, templates, and Tag Helpers with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-296">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-296">Pages work with all the capabilities of the :::no-loc(Razor)::: view engine.</span></span> <span data-ttu-id="5bcf9-297">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml* ve *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml* , and *_ViewImports.cshtml* work in the same way they do for conventional :::no-loc(Razor)::: views.</span></span>

<span data-ttu-id="5bcf9-298">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5bcf9-299">*Sayfa/paylaşılan/_Layout. cshtml* 'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml* :</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="5bcf9-300">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5bcf9-301">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5bcf9-302">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="5bcf9-303">:::no-loc(Razor):::Sayfanın içeriği `@RenderBody()` olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-303">The contents of the :::no-loc(Razor)::: page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="5bcf9-304">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="5bcf9-305">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml* ' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml* :</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5bcf9-306">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5bcf9-307">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5bcf9-308">*Sayfalar/paylaşılan* klasördeki bir düzen, :::no-loc(Razor)::: *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-308">A layout in the *Pages/Shared* folder can be used from any :::no-loc(Razor)::: page under the *Pages* folder.</span></span>

<span data-ttu-id="5bcf9-309">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5bcf9-310">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5bcf9-311">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5bcf9-312">:::no-loc(Razor)::: Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-312">:::no-loc(Razor)::: Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5bcf9-313">Bir sayfadan aramayı görüntüleme :::no-loc(Razor)::: *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-313">View search from a :::no-loc(Razor)::: Page includes the *Pages* folder.</span></span> <span data-ttu-id="5bcf9-314">MVC denetleyicileri ve geleneksel görünümler ile kullanılan düzenler, şablonlar ve partilar :::no-loc(Razor)::: *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-314">The layouts, templates, and partials used with MVC controllers and conventional :::no-loc(Razor)::: views *just work*.</span></span>

<span data-ttu-id="5bcf9-315">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5bcf9-316">`@namespace` , Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5bcf9-317">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5bcf9-318">`@namespace`Bir sayfada ayarlanan yönerge:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5bcf9-319">`@namespace`Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="5bcf9-320">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5bcf9-321">`@namespace`Yönerge *_ViewImports. cshtml* içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-321">When the `@namespace` directive is contained in *_ViewImports.cshtml* , the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5bcf9-322">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5bcf9-323">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5bcf9-324">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-325">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, :::no-loc(Razor)::: `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-325">The generated namespace for the *Pages/Customers/Edit.cshtml* :::no-loc(Razor)::: Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5bcf9-326">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir :::no-loc(Razor)::: .*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-326">`@namespace` *also works with conventional :::no-loc(Razor)::: views.*</span></span>

<span data-ttu-id="5bcf9-327">*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="5bcf9-328">Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="5bcf9-329">Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="5bcf9-330">Düzen dosyası JavaScript dosyalarını içeri aktardı.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="5bcf9-331">[ :::no-loc(Razor)::: Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml* , Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-331">The [:::no-loc(Razor)::: Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml* , which hooks up client-side validation.</span></span>

<span data-ttu-id="5bcf9-332">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="5bcf9-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5bcf9-333">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-333">URL generation for Pages</span></span>

<span data-ttu-id="5bcf9-334">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="5bcf9-335">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5bcf9-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-336">*/Pages*</span></span>

  * <span data-ttu-id="5bcf9-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="5bcf9-338">*Gizlilik. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="5bcf9-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-339">*/Customers*</span></span>

    * <span data-ttu-id="5bcf9-340">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="5bcf9-341">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5bcf9-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-342">*Index.cshtml*</span></span>

<span data-ttu-id="5bcf9-343">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="5bcf9-344">Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="5bcf9-345">*Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="5bcf9-346">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="5bcf9-347">Mutlak sayfa adı, `/Index` *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5bcf9-348">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5bcf9-349">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5bcf9-350">Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="5bcf9-351">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5bcf9-352">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5bcf9-353">Aşağıdaki tabloda, `RedirectToPage` *sayfalarda/müşteriler/Create. cshtml* 'de farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="5bcf9-354">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="5bcf9-354">RedirectToPage(x)</span></span>| <span data-ttu-id="5bcf9-355">Sayfa</span><span class="sxs-lookup"><span data-stu-id="5bcf9-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5bcf9-356">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5bcf9-357">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-357">*Pages/Index*</span></span> |
| <span data-ttu-id="5bcf9-358">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="5bcf9-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5bcf9-359">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5bcf9-360">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5bcf9-361">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-361">*Pages/Index*</span></span> |
| <span data-ttu-id="5bcf9-362">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5bcf9-363">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/:::no-loc(Razor):::PagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="5bcf9-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="5bcf9-365">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="5bcf9-366">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5bcf9-367">Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="5bcf9-368">Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="5bcf9-369">Klasör adını içermediği için bağlantılar kopuk değildir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="5bcf9-370">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5bcf9-371">Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5bcf9-372">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="5bcf9-372">ViewData attribute</span></span>

<span data-ttu-id="5bcf9-373">Veri, ile bir sayfaya geçirilebilir <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="5bcf9-374">Özniteliği olan Özellikler `[ViewData]` , değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="5bcf9-375">Aşağıdaki örnekte, `AboutModel` `[ViewData]` özniteliği `Title` özelliğine uygular:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="5bcf9-376">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5bcf9-377">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5bcf9-378">TempData</span><span class="sxs-lookup"><span data-stu-id="5bcf9-378">TempData</span></span>

<span data-ttu-id="5bcf9-379">ASP.NET Core, öğesini kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="5bcf9-380">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-380">This property stores data until it's read.</span></span> <span data-ttu-id="5bcf9-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5bcf9-382">`TempData` , bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5bcf9-383">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5bcf9-384">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5bcf9-385">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5bcf9-386">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5bcf9-387">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="5bcf9-387">Multiple handlers per page</span></span>

<span data-ttu-id="5bcf9-388">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="5bcf9-389">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5bcf9-390">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5bcf9-391">`asp-page-handler` bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5bcf9-392">`asp-page` örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5bcf9-393">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-393">The page model:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5bcf9-394">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini* kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5bcf9-395">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5bcf9-396">Yukarıdaki örnekte, Page metotları OnPost **Joinlist** Async ve onpost **Joinlıstuc** Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-396">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="5bcf9-397">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5bcf9-398">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5bcf9-399">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5bcf9-400">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-400">Custom routes</span></span>

<span data-ttu-id="5bcf9-401">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5bcf9-402">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-402">Specify a custom route to a page.</span></span> <span data-ttu-id="5bcf9-403">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5bcf9-404">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-404">Append segments to a page's default route.</span></span> <span data-ttu-id="5bcf9-405">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5bcf9-406">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="5bcf9-407">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5bcf9-408">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5bcf9-409">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5bcf9-410">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5bcf9-411">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-412">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5bcf9-413">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5bcf9-414">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="5bcf9-415">Gelişmiş yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-415">Advanced configuration and settings</span></span>

<span data-ttu-id="5bcf9-416">Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="5bcf9-417">Gelişmiş seçenekleri yapılandırmak için, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages%2A> yapılandıran aşırı yüklemeyi kullanın <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="5bcf9-418">Sayfalar için <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-418">Use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5bcf9-419">Kurallar hakkında daha fazla bilgi için bkz. [ :::no-loc(Razor)::: Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-419">For more information on conventions, see [:::no-loc(Razor)::: Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="5bcf9-420">Görünümleri önceden derlemek için bkz. [ :::no-loc(Razor)::: derlemeyi görüntüle](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-420">To precompile views, see [:::no-loc(Razor)::: view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="5bcf9-421">:::no-loc(Razor):::Sayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="5bcf9-421">Specify that :::no-loc(Razor)::: Pages are at the content root</span></span>

<span data-ttu-id="5bcf9-422">Varsayılan olarak, :::no-loc(Razor)::: Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-422">By default, :::no-loc(Razor)::: Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5bcf9-423"><xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.With:::no-loc(Razor):::PagesAtContentRoot*> :::no-loc(Razor)::: Sayfalarınızın, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) () olduğunu belirtmek için ekleyin <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-423">Add <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.With:::no-loc(Razor):::PagesAtContentRoot*> to specify that your :::no-loc(Razor)::: Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/StartupWith:::no-loc(Razor):::PagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5bcf9-424">:::no-loc(Razor):::Sayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="5bcf9-424">Specify that :::no-loc(Razor)::: Pages are at a custom root directory</span></span>

<span data-ttu-id="5bcf9-425"><xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcCoreBuilderExtensions.With:::no-loc(Razor):::PagesRoot*>Bu :::no-loc(Razor)::: sayfaların, uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-425">Add <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcCoreBuilderExtensions.With:::no-loc(Razor):::PagesRoot*> to specify that :::no-loc(Razor)::: Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/:::no-loc(Razor):::PagesContacts/StartupWith:::no-loc(Razor):::PagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5bcf9-426">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-426">Additional resources</span></span>

* <span data-ttu-id="5bcf9-427">Bu giriş hakkında daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-427">See [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="5bcf9-428">Özniteliği ve :::no-loc(Razor)::: sayfaları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="5bcf9-428">Authorize attribute and :::no-loc(Razor)::: Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="5bcf9-429">Örnek kodu indirme veya görüntüleme</span><span class="sxs-lookup"><span data-stu-id="5bcf9-429">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5bcf9-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="5bcf9-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="5bcf9-431">:::no-loc(Razor)::: Sayfalar, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir yönüdür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-431">:::no-loc(Razor)::: Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="5bcf9-432">Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="5bcf9-433">Bu belge, sayfalara giriş sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-433">This document provides an introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="5bcf9-434">Adım adım öğretici değildir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="5bcf9-435">Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-435">If you find some of the sections too advanced, see [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="5bcf9-436">ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5bcf9-437">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bcf9-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bcf9-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bcf9-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5bcf9-440">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="5bcf9-441">:::no-loc(Razor):::Sayfalar projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-441">Create a :::no-loc(Razor)::: Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bcf9-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5bcf9-443">Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-443">See [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a :::no-loc(Razor)::: Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5bcf9-444">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bcf9-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5bcf9-445">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="5bcf9-446">Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bcf9-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bcf9-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5bcf9-448">`dotnet new webapp`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="5bcf9-449">:::no-loc(Razor)::: Sayfaları</span><span class="sxs-lookup"><span data-stu-id="5bcf9-449">:::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-450">:::no-loc(Razor):::*Startup.cs* 'de sayfalar etkin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-450">:::no-loc(Razor)::: Pages is enabled in *Startup.cs* :</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="5bcf9-451">Temel bir sayfa düşünün: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5bcf9-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="5bcf9-452">Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ :::no-loc(Razor)::: görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-452">The preceding code looks a lot like a [:::no-loc(Razor)::: view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5bcf9-453">Bu, farklı kılan `@page` yönergedir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="5bcf9-454">`@page` dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5bcf9-455">`@page` sayfadaki ilk yönerge olmalıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-455">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="5bcf9-456">`@page` diğer yapıların davranışını etkiler :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-456">`@page` affects the behavior of other :::no-loc(Razor)::: constructs.</span></span>

<span data-ttu-id="5bcf9-457">Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5bcf9-458">*Pages/Index2. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5bcf9-459">*Pages/Index2. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5bcf9-460">Kural gereği, `PageModel` sınıf dosyası :::no-loc(Razor)::: *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-460">By convention, the `PageModel` class file has the same name as the :::no-loc(Razor)::: Page file with *.cs* appended.</span></span> <span data-ttu-id="5bcf9-461">Örneğin, önceki :::no-loc(Razor)::: sayfa *Pages/Index2. cshtml* ' dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-461">For example, the previous :::no-loc(Razor)::: Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5bcf9-462">Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5bcf9-463">URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5bcf9-464">Aşağıdaki tabloda, bir :::no-loc(Razor)::: sayfa yolu ve eşleşen URL gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-464">The following table shows a :::no-loc(Razor)::: Page path and the matching URL:</span></span>

| <span data-ttu-id="5bcf9-465">Dosya adı ve yolu</span><span class="sxs-lookup"><span data-stu-id="5bcf9-465">File name and path</span></span>               | <span data-ttu-id="5bcf9-466">eşleşen URL</span><span class="sxs-lookup"><span data-stu-id="5bcf9-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5bcf9-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5bcf9-468">`/` veya `/Index`</span><span class="sxs-lookup"><span data-stu-id="5bcf9-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="5bcf9-469">*/Pages/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5bcf9-470">*/Pages/Store/Contact.exe*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5bcf9-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5bcf9-472">`/Store` veya `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5bcf9-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5bcf9-473">Notlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-473">Notes:</span></span>

* <span data-ttu-id="5bcf9-474">Çalışma zamanı sayfalar :::no-loc(Razor)::: klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-474">The runtime looks for :::no-loc(Razor)::: Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5bcf9-475">`Index` , URL bir sayfa içermiyorsa varsayılan sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5bcf9-476">Temel form yazma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-476">Write a basic form</span></span>

<span data-ttu-id="5bcf9-477">:::no-loc(Razor)::: Sayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-477">:::no-loc(Razor)::: Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5bcf9-478">[Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a :::no-loc(Razor)::: Page class.</span></span> <span data-ttu-id="5bcf9-479">Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5bcf9-480">Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/:::no-loc(Razor):::PagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/:::no-loc(Razor):::PagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="5bcf9-481">Veri modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-481">The data model:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Data/Customer.cs)]

<span data-ttu-id="5bcf9-482">DB bağlamı:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-482">The db context:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="5bcf9-483">*Pages/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="5bcf9-484">*Pages/Create. cshtml. cs* sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5bcf9-485">Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5bcf9-486">`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5bcf9-487">Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5bcf9-488">Bu ayrım şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-488">This separation allows:</span></span>

* <span data-ttu-id="5bcf9-489">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="5bcf9-490">Sayfaların [birim testi](xref:test/razor-pages-tests) .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="5bcf9-491">Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi* vardır `POST` (bir Kullanıcı formu gönderdiğinde).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-491">The page has an `OnPostAsync` *handler method* , which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5bcf9-492">Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="5bcf9-493">En yaygın işleyiciler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-493">The most common handlers are:</span></span>

* <span data-ttu-id="5bcf9-494">Sayfa için gereken durumu başlatmak için `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5bcf9-495">[OnGet](#OnGet) örneği.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="5bcf9-496">Form gönderilerini işlemek için `OnPost`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5bcf9-497">`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5bcf9-498">Yukarıdaki kod, sayfalar için tipik bir davranıştır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-498">The preceding code is typical for :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="5bcf9-499">Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5bcf9-500">`OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5bcf9-501">[Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="5bcf9-502">Önceki `OnPostAsync` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5bcf9-503">Temel akışı `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5bcf9-504">Doğrulama hatalarını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-504">Check for validation errors.</span></span>

* <span data-ttu-id="5bcf9-505">Hata yoksa, verileri kaydedin ve yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5bcf9-506">Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5bcf9-507">İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="5bcf9-508">Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5bcf9-509">Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için yardımcı yöntemini çağırır `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="5bcf9-510">`RedirectToPage` , `RedirectToAction` veya ' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur `RedirectToRoute` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="5bcf9-511">Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5bcf9-512">`RedirectToPage` , [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="5bcf9-513">Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen), `OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="5bcf9-514">`Page`, bir `PageResult` örneği döndürür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="5bcf9-515">Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5bcf9-516">`PageResult` , bir işleyici yöntemi için varsayılan dönüş türüdür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5bcf9-517">Döndüren bir işleyici yöntemi `void` sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="5bcf9-518">`Customer`Özelliği `[BindProperty]` model bağlamayı kabul etmek için özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="5bcf9-519">:::no-loc(Razor)::: Sayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-519">:::no-loc(Razor)::: Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5bcf9-520">Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="5bcf9-521">Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5bcf9-522">Giriş sayfası ( *Index. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-522">The home page ( *Index.cshtml* ):</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="5bcf9-523">İlişkili `PageModel` Sınıf ( *Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-523">The associated `PageModel` class ( *Index.cshtml.cs* ):</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="5bcf9-524">*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="5bcf9-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5bcf9-526">Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5bcf9-527">Örneğin, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5bcf9-528">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="5bcf9-529">Etiket Yardımcıları tarafından etkinleştirilir `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="5bcf9-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="5bcf9-530">*Pages/Edit. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-531">İlk satır `@page "{id:int}"` yönergesini içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5bcf9-532">Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5bcf9-533">Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5bcf9-534">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5bcf9-535">*Pages/Edit. cshtml. cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="5bcf9-536">*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="5bcf9-537">Sil düğmesi HTML biçiminde işlendiğinde, `formaction` için parametreleri içerir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="5bcf9-538">Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5bcf9-539">`handler`Özniteliği tarafından belirtilen `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5bcf9-540">Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5bcf9-541">Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5bcf9-542">Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5bcf9-543">`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5bcf9-544">, Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="5bcf9-545">Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="5bcf9-546">`OnPostDeleteAsync`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5bcf9-547">Sorgu dizesinden öğesini kabul eder `id` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="5bcf9-548">*Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması içeriyorsa `"{id:int?}"` , `id` Rota verilerinden gelir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="5bcf9-549">İçin rota verileri, gibi `id` URI 'de belirtilmiştir `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="5bcf9-550">Müşteri iletişim için veritabanını sorgular `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5bcf9-551">Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="5bcf9-552">Veritabanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-552">The database is updated.</span></span>
* <span data-ttu-id="5bcf9-553">`RedirectToPage`Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="5bcf9-554">Sayfa özelliklerini gerektiği gibi işaretle</span><span class="sxs-lookup"><span data-stu-id="5bcf9-554">Mark page properties as required</span></span>

<span data-ttu-id="5bcf9-555">İçindeki Özellikler `PageModel` [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="5bcf9-556">Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5bcf9-557">OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri</span><span class="sxs-lookup"><span data-stu-id="5bcf9-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5bcf9-558">`HEAD` istekleri belirli bir kaynak için üstbilgileri almanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="5bcf9-559">`GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5bcf9-560">Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="5bcf9-561">ASP.NET Core 2,1 veya üzeri sürümlerde, :::no-loc(Razor)::: `OnGet` hiçbir işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-561">In ASP.NET Core 2.1 or later, :::no-loc(Razor)::: Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="5bcf9-562">Bu davranış, içindeki [Setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="5bcf9-563">Varsayılan Şablonlar `SetCompatibilityVersion` ASP.NET Core 2,1 ve 2,2 ' de çağrıyı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="5bcf9-564">`SetCompatibilityVersion`:::no-loc(Razor):::sayfa seçeneğini etkin bir `AllowMappingHeadRequestsToGetHandler` şekilde ayarlar `true` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-564">`SetCompatibilityVersion` effectively sets the :::no-loc(Razor)::: Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="5bcf9-565">İle tüm davranışlardan çıkmak yerine `SetCompatibilityVersion` , açıkça *belirli* davranışları kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="5bcf9-566">Aşağıdaki kod, isteklerin işleyiciye eşlenmesine izin vermek için ' de `HEAD` kullanılır `OnGet` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="5bcf9-567">XSRF/CSRF ve :::no-loc(Razor)::: Sayfalar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-567">XSRF/CSRF and :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-568">[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5bcf9-569">Antiforgery belirteci oluşturma ve doğrulama, sayfalara otomatik olarak eklenir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-569">Antiforgery token generation and validation are automatically included in :::no-loc(Razor)::: Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="5bcf9-570">Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="5bcf9-570">Using Layouts, partials, templates, and Tag Helpers with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5bcf9-571">Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-571">Pages work with all the capabilities of the :::no-loc(Razor)::: view engine.</span></span> <span data-ttu-id="5bcf9-572">Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml* , *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml* , *_ViewImports.cshtml* work in the same way they do for conventional :::no-loc(Razor)::: views.</span></span>

<span data-ttu-id="5bcf9-573">Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5bcf9-574">*Sayfa/paylaşılan/_Layout. cshtml* 'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml* :</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="5bcf9-575">[Düzen](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5bcf9-576">Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5bcf9-577">JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="5bcf9-578">Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="5bcf9-579">[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml* ' de ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml* :</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5bcf9-580">Düzen *Sayfalar/paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5bcf9-581">Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5bcf9-582">*Sayfalar/paylaşılan* klasördeki bir düzen, :::no-loc(Razor)::: *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-582">A layout in the *Pages/Shared* folder can be used from any :::no-loc(Razor)::: page under the *Pages* folder.</span></span>

<span data-ttu-id="5bcf9-583">Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5bcf9-584">Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5bcf9-585">*Görünümler/paylaşılan* bir MVC görünümleri modelidir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5bcf9-586">:::no-loc(Razor)::: Sayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-586">:::no-loc(Razor)::: Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5bcf9-587">Bir sayfadan aramayı görüntüleme :::no-loc(Razor)::: *sayfaları* klasörünü içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-587">View search from a :::no-loc(Razor)::: Page includes the *Pages* folder.</span></span> <span data-ttu-id="5bcf9-588">MVC denetleyicileri ve geleneksel görünümler ile kullanmakta olduğunuz düzenler, şablonlar ve parals :::no-loc(Razor)::: *yalnızca çalışır*.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-588">The layouts, templates, and partials you're using with MVC controllers and conventional :::no-loc(Razor)::: views *just work*.</span></span>

<span data-ttu-id="5bcf9-589">Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5bcf9-590">`@namespace` , Öğreticinin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5bcf9-591">`@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5bcf9-592">`@namespace`Yönerge bir sayfada açıkça kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5bcf9-593">Yönergesi sayfanın ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="5bcf9-594">`@model`Yönergesinin ad alanını içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5bcf9-595">`@namespace`Yönerge *_ViewImports. cshtml* içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-595">When the `@namespace` directive is contained in *_ViewImports.cshtml* , the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5bcf9-596">Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5bcf9-597">Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5bcf9-598">*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-599">*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, :::no-loc(Razor)::: `PageModel` sınıfıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-599">The generated namespace for the *Pages/Customers/Edit.cshtml* :::no-loc(Razor)::: Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5bcf9-600">`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir :::no-loc(Razor)::: .*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-600">`@namespace` *also works with conventional :::no-loc(Razor)::: views.*</span></span>

<span data-ttu-id="5bcf9-601">Özgün *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="5bcf9-602">Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="5bcf9-603">[ :::no-loc(Razor)::: Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml* , Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-603">The [:::no-loc(Razor)::: Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml* , which hooks up client-side validation.</span></span>

<span data-ttu-id="5bcf9-604">Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..</span><span class="sxs-lookup"><span data-stu-id="5bcf9-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5bcf9-605">Sayfalar için URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="5bcf9-605">URL generation for Pages</span></span>

<span data-ttu-id="5bcf9-606">`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="5bcf9-607">Uygulama aşağıdaki dosya/klasör yapısına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5bcf9-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-608">*/Pages*</span></span>

  * <span data-ttu-id="5bcf9-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="5bcf9-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-610">*/Customers*</span></span>

    * <span data-ttu-id="5bcf9-611">*. Cshtml oluştur*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="5bcf9-612">*Edit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5bcf9-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-613">*Index.cshtml*</span></span>

<span data-ttu-id="5bcf9-614">*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="5bcf9-615">Dize, `/Index` önceki sayfaya erişmek IÇIN URI 'nin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="5bcf9-616">Dize, `/Index` *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5bcf9-617">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5bcf9-618">Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5bcf9-619">Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="5bcf9-620">URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5bcf9-621">Sayfalar için URL oluşturma göreli adları destekler.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5bcf9-622">Aşağıdaki tabloda, `RedirectToPage` *sayfa/müşteri/oluşturma. cshtml* 'den farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml* :</span></span>

| <span data-ttu-id="5bcf9-623">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="5bcf9-623">RedirectToPage(x)</span></span>| <span data-ttu-id="5bcf9-624">Sayfa</span><span class="sxs-lookup"><span data-stu-id="5bcf9-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5bcf9-625">RedirectToPage ("/Index")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5bcf9-626">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-626">*Pages/Index*</span></span> |
| <span data-ttu-id="5bcf9-627">RedirectToPage ("./Index");</span><span class="sxs-lookup"><span data-stu-id="5bcf9-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5bcf9-628">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5bcf9-629">RedirectToPage (".. /İndex ")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5bcf9-630">*Sayfa/dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-630">*Pages/Index*</span></span> |
| <span data-ttu-id="5bcf9-631">RedirectToPage ("Dizin")</span><span class="sxs-lookup"><span data-stu-id="5bcf9-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5bcf9-632">*Sayfalar/müşteriler/Dizin*</span><span class="sxs-lookup"><span data-stu-id="5bcf9-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="5bcf9-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="5bcf9-634">`RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="5bcf9-635">Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5bcf9-636">Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="5bcf9-637">Tüm bağlantılar hala çalışır (klasör adını içermediği için).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="5bcf9-638">Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5bcf9-639">Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5bcf9-640">ViewData özniteliği</span><span class="sxs-lookup"><span data-stu-id="5bcf9-640">ViewData attribute</span></span>

<span data-ttu-id="5bcf9-641">Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="5bcf9-642">' Deki denetleyicilerde veya :::no-loc(Razor)::: sayfa modellerinde bulunan özelliklerin `[ViewData]` değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-642">Properties on controllers or :::no-loc(Razor)::: Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="5bcf9-643">Aşağıdaki örnekte, `AboutModel` `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="5bcf9-644">`Title`Özelliği hakkında sayfasının başlığına ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="5bcf9-645">Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5bcf9-646">Mizanpajda, başlık ViewData sözlüğünden okundu:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5bcf9-647">TempData</span><span class="sxs-lookup"><span data-stu-id="5bcf9-647">TempData</span></span>

<span data-ttu-id="5bcf9-648">ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="5bcf9-649">Bu özellik, okunana kadar verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-649">This property stores data until it's read.</span></span> <span data-ttu-id="5bcf9-650">`Keep`Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5bcf9-651">`TempData` , bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5bcf9-652">Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5bcf9-653">*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5bcf9-654">*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5bcf9-655">Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5bcf9-656">Sayfa başına birden çok işleyici</span><span class="sxs-lookup"><span data-stu-id="5bcf9-656">Multiple handlers per page</span></span>

<span data-ttu-id="5bcf9-657">Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="5bcf9-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a :::no-loc(Razor)::: page, even when there's no `asp-` attribute   -->

<span data-ttu-id="5bcf9-658">Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5bcf9-659">`asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5bcf9-660">`asp-page-handler` bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5bcf9-661">`asp-page` örnek geçerli sayfaya bağlandığından belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5bcf9-662">Sayfa modeli:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-662">The page model:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5bcf9-663">Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini* kullanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5bcf9-664">Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5bcf9-665">Yukarıdaki örnekte, Page metotları OnPost **Joinlist** Async ve onpost **Joinlıstuc** Async ' dir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-665">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="5bcf9-666">*Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5bcf9-667">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5bcf9-668">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5bcf9-669">Özel yollar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-669">Custom routes</span></span>

<span data-ttu-id="5bcf9-670">`@page`İçin yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5bcf9-671">Sayfaya özel bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-671">Specify a custom route to a page.</span></span> <span data-ttu-id="5bcf9-672">Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5bcf9-673">Kesimleri bir sayfanın varsayılan yoluna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-673">Append segments to a page's default route.</span></span> <span data-ttu-id="5bcf9-674">Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5bcf9-675">Bir sayfanın varsayılan yoluna parametreleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="5bcf9-676">Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5bcf9-677">Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5bcf9-678">Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5bcf9-679">URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5bcf9-680">Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/:::no-loc(Razor):::PagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5bcf9-681">Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5bcf9-682">' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5bcf9-683">`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="5bcf9-684">Yapılandırma ve ayarlar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-684">Configuration and settings</span></span>

<span data-ttu-id="5bcf9-685">Gelişmiş seçenekleri yapılandırmak için, `Add:::no-loc(Razor):::PagesOptions` MVC Oluşturucu 'da genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5bcf9-685">To configure advanced options, use the extension method `Add:::no-loc(Razor):::PagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/:::no-loc(Razor):::PagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="5bcf9-686">Şu anda `:::no-loc(Razor):::PagesOptions` ' nı, sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-686">Currently you can use the `:::no-loc(Razor):::PagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5bcf9-687">Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="5bcf9-688">Görünümleri önceden derlemek için bkz. [ :::no-loc(Razor)::: derlemeyi görüntüle](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="5bcf9-688">To precompile views, see [:::no-loc(Razor)::: view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="5bcf9-689">[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="5bcf9-690">Bu giriş hakkında daha fazla bilgi için bkz. [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5bcf9-690">See [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="5bcf9-691">:::no-loc(Razor):::Sayfaların içerik kökünde olduğunu belirtin</span><span class="sxs-lookup"><span data-stu-id="5bcf9-691">Specify that :::no-loc(Razor)::: Pages are at the content root</span></span>

<span data-ttu-id="5bcf9-692">Varsayılan olarak, :::no-loc(Razor)::: Sayfalar, */Pages* dizininde kök olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="5bcf9-692">By default, :::no-loc(Razor)::: Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5bcf9-693">Sayfalarınızın uygulamanın içerik kökünde (contentrootpath) olduğunu belirtmek için [ :::no-loc(Razor)::: pagesatcontentroot Ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) [addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ' ye ekleyin :::no-loc(Razor)::: : [content root](xref:fundamentals/index#content-root) [ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)</span><span class="sxs-lookup"><span data-stu-id="5bcf9-693">Add [With:::no-loc(Razor):::PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your :::no-loc(Razor)::: Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
    {
        ...
    })
    .With:::no-loc(Razor):::PagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5bcf9-694">:::no-loc(Razor):::Sayfaların özel kök dizinde olduğunu belirtme</span><span class="sxs-lookup"><span data-stu-id="5bcf9-694">Specify that :::no-loc(Razor)::: Pages are at a custom root directory</span></span>

<span data-ttu-id="5bcf9-695">Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye [ :::no-loc(Razor)::: pagesroot ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin :::no-loc(Razor)::: (göreli bir yol sağlayın):</span><span class="sxs-lookup"><span data-stu-id="5bcf9-695">Add [With:::no-loc(Razor):::PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your :::no-loc(Razor)::: Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
    {
        ...
    })
    .With:::no-loc(Razor):::PagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="5bcf9-696">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5bcf9-696">Additional resources</span></span>

* [<span data-ttu-id="5bcf9-697">Özniteliği ve :::no-loc(Razor)::: sayfaları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="5bcf9-697">Authorize attribute and :::no-loc(Razor)::: Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
