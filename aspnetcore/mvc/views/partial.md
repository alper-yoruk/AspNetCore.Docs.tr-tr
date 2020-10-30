---
title: ASP.NET Core kısmi görünümler
author: ardalis
description: Büyük biçimlendirme dosyalarını bölmek ve ASP.NET Core uygulamalarında Web sayfalarında ortak biçimlendirmenin çoğaltılmasını azaltmak için kısmi görünümleri nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 01fb87205f7855f0995cbcd135b6b01f15835e3b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060605"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="065dd-103">ASP.NET Core kısmi görünümler</span><span class="sxs-lookup"><span data-stu-id="065dd-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="065dd-104">[Steve Smith](https://ardalis.com/), [Maa jendoubi](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="065dd-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="065dd-105">Kısmi görünüm, başka bir [:::no-loc(Razor):::](xref:mvc/views/razor) *.cshtml* [`@page`](xref:mvc/views/razor#page) işaretleme dosyasının işlenmiş çıktısındaki HTML çıkışını işleyen bir yönerge olmadan bir işaretleme *within* dosyasıdır (. cshtml).</span><span class="sxs-lookup"><span data-stu-id="065dd-105">A partial view is a [:::no-loc(Razor):::](xref:mvc/views/razor) markup file ( *.cshtml* ) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-106">*Kısmi görünüm* terimi, biçimlendirme dosyaları *Görünümler* olarak adlandırılan bir MVC uygulaması veya :::no-loc(Razor)::: biçimlendirme dosyalarının *Sayfalar* olarak adlandırıldığını bir sayfalar uygulaması geliştirirken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="065dd-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views* , or a :::no-loc(Razor)::: Pages app, where markup files are called *pages* .</span></span> <span data-ttu-id="065dd-107">Bu konu genel olarak, MVC görünümleri ve :::no-loc(Razor)::: Sayfalar sayfalarını *biçimlendirme dosyaları* olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="065dd-107">This topic generically refers to MVC views and :::no-loc(Razor)::: Pages pages as *markup files* .</span></span>

::: moniker-end

<span data-ttu-id="065dd-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="065dd-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="065dd-109">Kısmi görünümlerin ne zaman kullanılacağı</span><span class="sxs-lookup"><span data-stu-id="065dd-109">When to use partial views</span></span>

<span data-ttu-id="065dd-110">Kısmi görünümler şu şekilde etkili bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="065dd-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="065dd-111">Büyük biçimlendirme dosyalarını daha küçük bileşenlere bölün.</span><span class="sxs-lookup"><span data-stu-id="065dd-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="065dd-112">Birkaç mantıksal parçadan oluşan büyük, karmaşık bir biçimlendirme dosyasında, her bir parçada kısmi bir görünümde yalıtılmış olarak çalışmanın bir avantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="065dd-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="065dd-113">Biçimlendirme dosyasındaki kod, biçimlendirme yalnızca genel sayfa yapısını ve kısmi görünümlere yönelik başvuruları içerdiğinden yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="065dd-114">Biçimlendirme dosyaları arasında ortak biçimlendirme içeriğinin çoğaltılmasını azaltın.</span><span class="sxs-lookup"><span data-stu-id="065dd-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="065dd-115">Biçimlendirme dosyalarında aynı biçimlendirme öğeleri kullanıldığında, kısmi bir görünüm biçimlendirme içeriğinin tek bir kısmi görünüm dosyasına çoğaltılmasını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="065dd-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="065dd-116">Kısmi görünümdeki biçimlendirme değiştirildiğinde, kısmi görünümü kullanan biçimlendirme dosyalarının işlenmiş çıkışını günceller.</span><span class="sxs-lookup"><span data-stu-id="065dd-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="065dd-117">Yaygın düzen öğelerini korumak için kısmi görünümler kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="065dd-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="065dd-118">Ortak düzen öğeleri [_Layout. cshtml](xref:mvc/views/layout) dosyalarında belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="065dd-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="065dd-119">Biçimlendirmeyi işlemek için karmaşık işleme mantığının veya kod yürütmenin gerekli olduğu kısmi bir görünüm kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="065dd-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="065dd-120">Kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components)kullanın.</span><span class="sxs-lookup"><span data-stu-id="065dd-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="065dd-121">Kısmi görünümler bildirme</span><span class="sxs-lookup"><span data-stu-id="065dd-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="065dd-122">Kısmi görünüm, *.cshtml* [`@page`](xref:mvc/views/razor#page) *Görünümler* klasörü (MVC) veya *Sayfalar* klasörü (sayfalar) içinde bir yönerge olmadan bir. cshtml biçimlendirme dosyasıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="065dd-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (:::no-loc(Razor)::: Pages).</span></span>

<span data-ttu-id="065dd-123">ASP.NET Core MVC 'de, denetleyici <xref:Microsoft.AspNetCore.Mvc.ViewResult> bir görünüm veya kısmi görünüm döndürmektedir.</span><span class="sxs-lookup"><span data-stu-id="065dd-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="065dd-124">:::no-loc(Razor):::Sayfalarda, bir <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> nesne olarak temsil edilen kısmi bir görünüm döndürebilir <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> .</span><span class="sxs-lookup"><span data-stu-id="065dd-124">In :::no-loc(Razor)::: Pages, a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="065dd-125">Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="065dd-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="065dd-126">MVC görünümü veya sayfa işleme farklı olarak, kısmi bir görünüm *_ViewStart. cshtml* çalıştırmaz.</span><span class="sxs-lookup"><span data-stu-id="065dd-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="065dd-127">*_ViewStart. cshtml* hakkında daha fazla bilgi için bkz <xref:mvc/views/layout> ..</span><span class="sxs-lookup"><span data-stu-id="065dd-127">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="065dd-128">Kısmi görünüm dosya adları genellikle bir alt çizgi () ile başlar `_` .</span><span class="sxs-lookup"><span data-stu-id="065dd-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="065dd-129">Bu adlandırma kuralı gerekli değildir, ancak görünüm ve sayfalardan kısmi görünümleri görsel açıdan ayırt etmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="065dd-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="065dd-130">Kısmi görünüm, *Görünümler* klasörü içinde tutulan bir *. cshtml* biçimlendirme dosyasıdır.</span><span class="sxs-lookup"><span data-stu-id="065dd-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="065dd-131">Denetleyicinin <xref:Microsoft.AspNetCore.Mvc.ViewResult> bir görünüm veya kısmi görünüm döndürme özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="065dd-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="065dd-132">Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="065dd-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="065dd-133">MVC görünüm işlemenin aksine, kısmi bir görünüm *_ViewStart. cshtml* çalıştırmaz.</span><span class="sxs-lookup"><span data-stu-id="065dd-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="065dd-134">*_ViewStart. cshtml* hakkında daha fazla bilgi için bkz <xref:mvc/views/layout> ..</span><span class="sxs-lookup"><span data-stu-id="065dd-134">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="065dd-135">Kısmi görünüm dosya adları genellikle bir alt çizgi () ile başlar `_` .</span><span class="sxs-lookup"><span data-stu-id="065dd-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="065dd-136">Bu adlandırma kuralı gerekli değildir, ancak kısmen görünümlerini görünümlerde görsel açıdan ayırt etmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="065dd-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="065dd-137">Kısmi görünüme başvur</span><span class="sxs-lookup"><span data-stu-id="065dd-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="065dd-138">Sayfalarda PageModel içinde kısmi bir görünüm kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="065dd-138">Use a partial view in a :::no-loc(Razor)::: Pages PageModel</span></span>

<span data-ttu-id="065dd-139">ASP.NET Core 2,0 veya 2,1 ' de, aşağıdaki işleyici yöntemi *\_ authorpartialrp. cshtml* kısmi görünümünü yanıta işler:</span><span class="sxs-lookup"><span data-stu-id="065dd-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="065dd-140">ASP.NET Core 2,2 veya sonraki sürümlerde, bir işleyici yöntemi alternatif olarak <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*> bir nesnesi oluşturmak için yöntemini çağırabilir `PartialViewResult` :</span><span class="sxs-lookup"><span data-stu-id="065dd-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="065dd-141">Biçimlendirme dosyasında kısmi görünüm kullanma</span><span class="sxs-lookup"><span data-stu-id="065dd-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-142">Bir biçimlendirme dosyasında kısmi bir görünüme başvurmak için birkaç yol vardır.</span><span class="sxs-lookup"><span data-stu-id="065dd-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="065dd-143">Uygulamaların aşağıdaki zaman uyumsuz işleme yaklaşımlardan birini kullanmasını öneririz:</span><span class="sxs-lookup"><span data-stu-id="065dd-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="065dd-144">Kısmi Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="065dd-145">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="065dd-146">Bir biçimlendirme dosyasında, kısmi bir görünüme başvurmak için iki yol vardır:</span><span class="sxs-lookup"><span data-stu-id="065dd-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="065dd-147">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="065dd-148">Zaman uyumlu HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="065dd-149">Uygulamaların [zaman uyumsuz HTML yardımcısını](#asynchronous-html-helper)kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="065dd-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="065dd-150">Kısmi Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-150">Partial Tag Helper</span></span>

<span data-ttu-id="065dd-151">[Kısmi etiket yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) ASP.NET Core 2,1 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="065dd-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="065dd-152">Kısmi etiket Yardımcısı içeriği zaman uyumsuz olarak işler ve HTML benzeri bir sözdizimi kullanır:</span><span class="sxs-lookup"><span data-stu-id="065dd-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="065dd-153">Bir dosya uzantısı mevcut olduğunda, etiket Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="065dd-154">Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur.</span><span class="sxs-lookup"><span data-stu-id="065dd-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="065dd-155">Bir tilde işareti ( `~/` ) veya eğik çizgi () ile başlayan yollar `/` uygulama köküne başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="065dd-156">**:::no-loc(Razor)::: Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="065dd-156">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="065dd-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="065dd-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="065dd-158">Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="065dd-159">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="065dd-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="065dd-160">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="065dd-161">Bir HTML Yardımcısı kullanırken en iyi yöntem kullanılır <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="065dd-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="065dd-162">`PartialAsync`<xref:Microsoft.AspNetCore.Html.IHtmlContent>içinde Sarmalanan bir tür döndürür <xref:System.Threading.Tasks.Task%601> .</span><span class="sxs-lookup"><span data-stu-id="065dd-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="065dd-163">Yöntemine, beklenen çağrının bir karakterle önek olarak eklenerek başvurulur `@` :</span><span class="sxs-lookup"><span data-stu-id="065dd-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="065dd-164">Dosya uzantısı varsa, HTML Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="065dd-165">Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur.</span><span class="sxs-lookup"><span data-stu-id="065dd-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="065dd-166">Bir tilde işareti ( `~/` ) veya eğik çizgi () ile başlayan yollar `/` uygulama köküne başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-167">**:::no-loc(Razor)::: Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="065dd-167">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="065dd-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="065dd-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="065dd-169">Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="065dd-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="065dd-170">Alternatif olarak, ile kısmi bir görünüm işleyebilirsiniz <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="065dd-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="065dd-171">Bu yöntem bir döndürmez <xref:Microsoft.AspNetCore.Html.IHtmlContent> .</span><span class="sxs-lookup"><span data-stu-id="065dd-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="065dd-172">İşlenmiş çıktıyı doğrudan yanıta akıp.</span><span class="sxs-lookup"><span data-stu-id="065dd-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="065dd-173">Yöntem bir sonuç döndürmediğinden, bir :::no-loc(Razor)::: kod bloğu içinde çağrılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="065dd-173">Because the method doesn't return a result, it must be called within a :::no-loc(Razor)::: code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="065dd-174">`RenderPartialAsync`, İçeriği oluşturduğundan, bazı senaryolarda daha iyi performans sağlar.</span><span class="sxs-lookup"><span data-stu-id="065dd-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="065dd-175">Performans açısından kritik durumlarda, her iki yaklaşımı kullanarak sayfayı kıyaslar ve daha hızlı bir yanıt üreten yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="065dd-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="065dd-176">Zaman uyumlu HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="065dd-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="065dd-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> ve <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sırasıyla zaman uyumlu eşdeğerlerdir `PartialAsync` `RenderPartialAsync` .</span><span class="sxs-lookup"><span data-stu-id="065dd-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="065dd-178">Zaman uyumlu eşdeğerleri, kilitlendikleri senaryolar olduğu için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="065dd-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="065dd-179">Zaman uyumlu yöntemler gelecek sürümlerde kaldırılmak üzere hedeflenmiştir.</span><span class="sxs-lookup"><span data-stu-id="065dd-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="065dd-180">Kodu yürütmeniz gerekiyorsa, kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components) kullanın.</span><span class="sxs-lookup"><span data-stu-id="065dd-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-181">`Partial` `RenderPartial` Bir Visual Studio Çözümleyicisi uyarısıyla çağırma veya sonuç.</span><span class="sxs-lookup"><span data-stu-id="065dd-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="065dd-182">Örneğin, varlığı `Partial` aşağıdaki uyarı iletisini verir:</span><span class="sxs-lookup"><span data-stu-id="065dd-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="065dd-183">Ihtmlhelper. Partial kullanımı uygulama kilitlenmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="065dd-184">&lt;Kısmi &gt; etiket Yardımcısı veya ıhtmlhelper. Partıalasync kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="065dd-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="065dd-185">Çağrıları `@Html.Partial` ile `@await Html.PartialAsync` veya [kısmi etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="065dd-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="065dd-186">Kısmi etiket Yardımcısı geçişi hakkında daha fazla bilgi için bkz. [HTML Yardımcısı 'Ndan geçiş](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="065dd-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="065dd-187">Kısmi görünüm bulma</span><span class="sxs-lookup"><span data-stu-id="065dd-187">Partial view discovery</span></span>

<span data-ttu-id="065dd-188">Bir dosya uzantısı olmayan kısmi bir görünüme ad ile başvurulduğunda, aşağıdaki konumlar belirtilen sırada aranır:</span><span class="sxs-lookup"><span data-stu-id="065dd-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-189">**:::no-loc(Razor)::: Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="065dd-189">**:::no-loc(Razor)::: Pages**</span></span>

1. <span data-ttu-id="065dd-190">Şu anda sayfanın klasörü yürütülüyor</span><span class="sxs-lookup"><span data-stu-id="065dd-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="065dd-191">Sayfanın klasörünün üzerindeki Dizin grafiği</span><span class="sxs-lookup"><span data-stu-id="065dd-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="065dd-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="065dd-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="065dd-193">Kısmi görünüm bulma için aşağıdaki kurallar geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="065dd-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="065dd-194">Kısmi görünümler farklı klasörlerde olduğunda aynı dosya adına sahip farklı kısmi görünümlere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="065dd-195">Dosya uzantısı olmadan kısmi bir görünüme ada göre başvurulması ve kısmi görünümün hem arayanın klasöründe hem de *paylaşılan* klasörde mevcut olması halinde, çağıranın klasöründeki kısmi görünüm kısmi görünümü sağlar.</span><span class="sxs-lookup"><span data-stu-id="065dd-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="065dd-196">Kısmi görünüm çağıranın klasöründe yoksa, kısmi görünüm *paylaşılan* klasörden sağlanır.</span><span class="sxs-lookup"><span data-stu-id="065dd-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="065dd-197">*Paylaşılan* klasördeki kısmi görünümler, *paylaşılan kısmi görünümler* veya *varsayılan kısmi görünümler* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="065dd-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views* .</span></span>
* <span data-ttu-id="065dd-198">Kısmi Görünümler *zincirleme* olabilir kısmi &mdash; Görünüm, çağrılar tarafından bir döngüsel başvuru oluşturulmadığı durumlarda başka bir kısmi görünümü çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="065dd-199">Göreli yollar her zaman geçerli dosyaya göredir, dosyanın köküne veya üst öğesine göre değil.</span><span class="sxs-lookup"><span data-stu-id="065dd-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="065dd-200">[:::no-loc(Razor):::](xref:mvc/views/razor) `section` Kısmi görünümde tanımlanan bir öğe, üst biçimlendirme dosyaları için görünmez değildir.</span><span class="sxs-lookup"><span data-stu-id="065dd-200">A [:::no-loc(Razor):::](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="065dd-201">`section`Yalnızca tanımlandığı kısmi görünüm için görülebilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="065dd-202">Kısmi görünümlerde verilere erişin</span><span class="sxs-lookup"><span data-stu-id="065dd-202">Access data from partial views</span></span>

<span data-ttu-id="065dd-203">Kısmi bir görünüm örneği oluşturulduğunda, üst öğenin sözlüğünün bir *kopyasını* alır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="065dd-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="065dd-204">Kısmi görünüm içindeki verilerde yapılan güncelleştirmeler üst görünümde kalıcı değildir.</span><span class="sxs-lookup"><span data-stu-id="065dd-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="065dd-205">`ViewData` kısmi görünüm geri döndüğünde kısmi görünümdeki değişiklikler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="065dd-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="065dd-206">Aşağıdaki örnek, bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) örneğinin kısmi bir görünüme nasıl geçirileceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="065dd-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="065dd-207">Bir modeli kısmi bir görünüme geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="065dd-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="065dd-208">Model özel bir nesne olabilir.</span><span class="sxs-lookup"><span data-stu-id="065dd-208">The model can be a custom object.</span></span> <span data-ttu-id="065dd-209">Bir modeli ile geçirebilirsiniz `PartialAsync` (bir içerik bloğunu çağırana kaydedebilir) veya `RenderPartialAsync` (içeriği çıkışa akıp):</span><span class="sxs-lookup"><span data-stu-id="065dd-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="065dd-210">**:::no-loc(Razor)::: Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="065dd-210">**:::no-loc(Razor)::: Pages**</span></span>

<span data-ttu-id="065dd-211">Örnek uygulamada aşağıdaki biçimlendirme, *Pages/ArticlesRP/ReadRP. cshtml* sayfasından yapılır.</span><span class="sxs-lookup"><span data-stu-id="065dd-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="065dd-212">Sayfada iki kısmi görünüm bulunur.</span><span class="sxs-lookup"><span data-stu-id="065dd-212">The page contains two partial views.</span></span> <span data-ttu-id="065dd-213">İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer.</span><span class="sxs-lookup"><span data-stu-id="065dd-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="065dd-214">`ViewDataDictionary`Oluşturucu aşırı yüklemesi, `ViewData` var olan sözlüğü korurken yeni bir sözlüğü geçirmek için kullanılır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="065dd-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="065dd-215">*Pages/Shared/_AuthorPartialRP. cshtml* , *readrp. cshtml* işaretleme dosyası tarafından başvurulan ilk kısmi görünümüdür:</span><span class="sxs-lookup"><span data-stu-id="065dd-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="065dd-216">*Pages/ArticlesRP/_ArticleSectionRP. cshtml* , *readrp. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümüdür:</span><span class="sxs-lookup"><span data-stu-id="065dd-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="065dd-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="065dd-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="065dd-218">Örnek uygulamada aşağıdaki biçimlendirme *görünümleri/makaleleri/Read. cshtml* görünümünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="065dd-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="065dd-219">Görünüm iki kısmi görünüm içerir.</span><span class="sxs-lookup"><span data-stu-id="065dd-219">The view contains two partial views.</span></span> <span data-ttu-id="065dd-220">İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer.</span><span class="sxs-lookup"><span data-stu-id="065dd-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="065dd-221">`ViewDataDictionary`Oluşturucu aşırı yüklemesi, `ViewData` var olan sözlüğü korurken yeni bir sözlüğü geçirmek için kullanılır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="065dd-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="065dd-222">*Views/Shared/_AuthorPartial. cshtml* , *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ilk kısmi görünümdür:</span><span class="sxs-lookup"><span data-stu-id="065dd-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="065dd-223">*Görünümler/makaleler/_ArticleSection. cshtml* *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümdür:</span><span class="sxs-lookup"><span data-stu-id="065dd-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="065dd-224">Çalışma zamanında, partiler, kendisini paylaşılan *_Layout. cshtml* içinde işlenen üst biçimlendirme dosyasının işlenmiş çıktısına işlenir.</span><span class="sxs-lookup"><span data-stu-id="065dd-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml* .</span></span> <span data-ttu-id="065dd-225">İlk kısmi görünüm, makalenin adını ve yayımlama tarihini işler:</span><span class="sxs-lookup"><span data-stu-id="065dd-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="065dd-226">Abrayhelincoln</span><span class="sxs-lookup"><span data-stu-id="065dd-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="065dd-227">&lt;Paylaşılan kısmi görünüm dosyası yolundan bu kısmi görünüm &gt; .</span><span class="sxs-lookup"><span data-stu-id="065dd-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="065dd-228">11/19/1863 12:00:00</span><span class="sxs-lookup"><span data-stu-id="065dd-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="065dd-229">İkinci kısmi görünüm, makalenin bölümlerini işler:</span><span class="sxs-lookup"><span data-stu-id="065dd-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="065dd-230">Bölüm bir dizin: 0</span><span class="sxs-lookup"><span data-stu-id="065dd-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="065dd-231">Dört puan ve yedi yıl önce...</span><span class="sxs-lookup"><span data-stu-id="065dd-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="065dd-232">Bölüm Iki Dizin: 1</span><span class="sxs-lookup"><span data-stu-id="065dd-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="065dd-233">Artık harika bir hukuki War, test ediyor...</span><span class="sxs-lookup"><span data-stu-id="065dd-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="065dd-234">Bölüm üç Dizin: 2</span><span class="sxs-lookup"><span data-stu-id="065dd-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="065dd-235">Ancak, daha büyük bir fikir için ayıramıyoruz...</span><span class="sxs-lookup"><span data-stu-id="065dd-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="065dd-236">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="065dd-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
