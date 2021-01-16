---
title: ASP.NET Core kısmi görünümler
author: ardalis
description: Büyük biçimlendirme dosyalarını bölmek ve ASP.NET Core uygulamalarında Web sayfalarında ortak biçimlendirmenin çoğaltılmasını azaltmak için kısmi görünümleri nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 3a7b054adc50be57028dfa0364f80ae8733b02f7
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252714"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="98594-103">ASP.NET Core kısmi görünümler</span><span class="sxs-lookup"><span data-stu-id="98594-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="98594-104">[Steve Smith](https://ardalis.com/), [Maa jendoubi](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="98594-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="98594-105">Kısmi görünüm, başka bir [Razor](xref:mvc/views/razor)  [`@page`](xref:mvc/views/razor#page) işaretleme dosyasının işlenmiş çıktısındaki HTML çıkışını işleyen bir yönerge olmadan bir işaretleme  dosyasıdır (. cshtml).</span><span class="sxs-lookup"><span data-stu-id="98594-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-106">*Kısmi görünüm* terimi, biçimlendirme dosyaları *Görünümler* olarak adlandırılan bir MVC uygulaması veya Razor biçimlendirme dosyalarının *Sayfalar* olarak adlandırıldığını bir sayfalar uygulaması geliştirirken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="98594-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="98594-107">Bu konu genel olarak, MVC görünümleri ve Razor Sayfalar sayfalarını *biçimlendirme dosyaları* olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="98594-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="98594-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="98594-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="98594-109">Kısmi görünümlerin ne zaman kullanılacağı</span><span class="sxs-lookup"><span data-stu-id="98594-109">When to use partial views</span></span>

<span data-ttu-id="98594-110">Kısmi görünümler şu şekilde etkili bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="98594-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="98594-111">Büyük biçimlendirme dosyalarını daha küçük bileşenlere bölün.</span><span class="sxs-lookup"><span data-stu-id="98594-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="98594-112">Birkaç mantıksal parçadan oluşan büyük, karmaşık bir biçimlendirme dosyasında, her bir parçada kısmi bir görünümde yalıtılmış olarak çalışmanın bir avantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="98594-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="98594-113">Biçimlendirme dosyasındaki kod, biçimlendirme yalnızca genel sayfa yapısını ve kısmi görünümlere yönelik başvuruları içerdiğinden yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="98594-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="98594-114">Biçimlendirme dosyaları arasında ortak biçimlendirme içeriğinin çoğaltılmasını azaltın.</span><span class="sxs-lookup"><span data-stu-id="98594-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="98594-115">Biçimlendirme dosyalarında aynı biçimlendirme öğeleri kullanıldığında, kısmi bir görünüm biçimlendirme içeriğinin tek bir kısmi görünüm dosyasına çoğaltılmasını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="98594-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="98594-116">Kısmi görünümdeki biçimlendirme değiştirildiğinde, kısmi görünümü kullanan biçimlendirme dosyalarının işlenmiş çıkışını günceller.</span><span class="sxs-lookup"><span data-stu-id="98594-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="98594-117">Yaygın düzen öğelerini korumak için kısmi görünümler kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="98594-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="98594-118">Ortak düzen öğeleri [_Layout. cshtml](xref:mvc/views/layout) dosyalarında belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="98594-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="98594-119">Biçimlendirmeyi işlemek için karmaşık işleme mantığının veya kod yürütmenin gerekli olduğu kısmi bir görünüm kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="98594-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="98594-120">Kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components)kullanın.</span><span class="sxs-lookup"><span data-stu-id="98594-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="98594-121">Kısmi görünümler bildirme</span><span class="sxs-lookup"><span data-stu-id="98594-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="98594-122">Kısmi görünüm,  [`@page`](xref:mvc/views/razor#page) *Görünümler* klasörü (MVC) veya *Sayfalar* klasörü (sayfalar) içinde bir yönerge olmadan bir. cshtml biçimlendirme dosyasıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="98594-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="98594-123">ASP.NET Core MVC 'de, denetleyici <xref:Microsoft.AspNetCore.Mvc.ViewResult> bir görünüm veya kısmi görünüm döndürmektedir.</span><span class="sxs-lookup"><span data-stu-id="98594-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="98594-124">RazorSayfalarda, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nesne olarak temsil edilen kısmi bir görünüm döndürebilir <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> .</span><span class="sxs-lookup"><span data-stu-id="98594-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="98594-125">Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="98594-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="98594-126">MVC görünümü veya sayfa işleme farklı olarak, kısmi bir görünüm *_ViewStart. cshtml* çalıştırmaz.</span><span class="sxs-lookup"><span data-stu-id="98594-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="98594-127">*_ViewStart. cshtml* hakkında daha fazla bilgi için bkz <xref:mvc/views/layout> ..</span><span class="sxs-lookup"><span data-stu-id="98594-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="98594-128">Kısmi görünüm dosya adları genellikle bir alt çizgi () ile başlar `_` .</span><span class="sxs-lookup"><span data-stu-id="98594-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="98594-129">Bu adlandırma kuralı gerekli değildir, ancak görünüm ve sayfalardan kısmi görünümleri görsel açıdan ayırt etmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="98594-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="98594-130">Kısmi görünüm, *Görünümler* klasörü içinde tutulan bir *. cshtml* biçimlendirme dosyasıdır.</span><span class="sxs-lookup"><span data-stu-id="98594-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="98594-131">Denetleyicinin <xref:Microsoft.AspNetCore.Mvc.ViewResult> bir görünüm veya kısmi görünüm döndürme özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="98594-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="98594-132">Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="98594-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="98594-133">MVC görünüm işlemenin aksine, kısmi bir görünüm *_ViewStart. cshtml* çalıştırmaz.</span><span class="sxs-lookup"><span data-stu-id="98594-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="98594-134">*_ViewStart. cshtml* hakkında daha fazla bilgi için bkz <xref:mvc/views/layout> ..</span><span class="sxs-lookup"><span data-stu-id="98594-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="98594-135">Kısmi görünüm dosya adları genellikle bir alt çizgi () ile başlar `_` .</span><span class="sxs-lookup"><span data-stu-id="98594-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="98594-136">Bu adlandırma kuralı gerekli değildir, ancak kısmen görünümlerini görünümlerde görsel açıdan ayırt etmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="98594-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="98594-137">Kısmi görünüme başvur</span><span class="sxs-lookup"><span data-stu-id="98594-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="98594-138">Sayfalarda PageModel içinde kısmi bir görünüm kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="98594-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="98594-139">ASP.NET Core 2,0 veya 2,1 ' de, aşağıdaki işleyici yöntemi *\_ authorpartialrp. cshtml* kısmi görünümünü yanıta işler:</span><span class="sxs-lookup"><span data-stu-id="98594-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

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

<span data-ttu-id="98594-140">ASP.NET Core 2,2 veya sonraki sürümlerde, bir işleyici yöntemi alternatif olarak <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> bir nesnesi oluşturmak için yöntemini çağırabilir `PartialViewResult` :</span><span class="sxs-lookup"><span data-stu-id="98594-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="98594-141">Biçimlendirme dosyasında kısmi görünüm kullanma</span><span class="sxs-lookup"><span data-stu-id="98594-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-142">Bir biçimlendirme dosyasında kısmi bir görünüme başvurmak için birkaç yol vardır.</span><span class="sxs-lookup"><span data-stu-id="98594-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="98594-143">Uygulamaların aşağıdaki zaman uyumsuz işleme yaklaşımlardan birini kullanmasını öneririz:</span><span class="sxs-lookup"><span data-stu-id="98594-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="98594-144">Kısmi Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="98594-145">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="98594-146">Bir biçimlendirme dosyasında, kısmi bir görünüme başvurmak için iki yol vardır:</span><span class="sxs-lookup"><span data-stu-id="98594-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="98594-147">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="98594-148">Zaman uyumlu HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="98594-149">Uygulamaların [zaman uyumsuz HTML yardımcısını](#asynchronous-html-helper)kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="98594-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="98594-150">Kısmi Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-150">Partial Tag Helper</span></span>

<span data-ttu-id="98594-151">[Kısmi etiket yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) ASP.NET Core 2,1 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="98594-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="98594-152">Kısmi etiket Yardımcısı içeriği zaman uyumsuz olarak işler ve HTML benzeri bir sözdizimi kullanır:</span><span class="sxs-lookup"><span data-stu-id="98594-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="98594-153">Bir dosya uzantısı mevcut olduğunda, etiket Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="98594-154">Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur.</span><span class="sxs-lookup"><span data-stu-id="98594-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="98594-155">Bir tilde işareti ( `~/` ) veya eğik çizgi () ile başlayan yollar `/` uygulama köküne başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="98594-156">**Razor Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="98594-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="98594-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="98594-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="98594-158">Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="98594-159">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="98594-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="98594-160">Zaman uyumsuz HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="98594-161">Bir HTML Yardımcısı kullanırken en iyi yöntem kullanılır <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="98594-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="98594-162">`PartialAsync`<xref:Microsoft.AspNetCore.Html.IHtmlContent>içinde Sarmalanan bir tür döndürür <xref:System.Threading.Tasks.Task%601> .</span><span class="sxs-lookup"><span data-stu-id="98594-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="98594-163">Yöntemine, beklenen çağrının bir karakterle önek olarak eklenerek başvurulur `@` :</span><span class="sxs-lookup"><span data-stu-id="98594-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="98594-164">Dosya uzantısı varsa, HTML Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="98594-165">Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur.</span><span class="sxs-lookup"><span data-stu-id="98594-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="98594-166">Bir tilde işareti ( `~/` ) veya eğik çizgi () ile başlayan yollar `/` uygulama köküne başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-167">**Razor Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="98594-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="98594-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="98594-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="98594-169">Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:</span><span class="sxs-lookup"><span data-stu-id="98594-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="98594-170">Alternatif olarak, ile kısmi bir görünüm işleyebilirsiniz <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="98594-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="98594-171">Bu yöntem bir döndürmez <xref:Microsoft.AspNetCore.Html.IHtmlContent> .</span><span class="sxs-lookup"><span data-stu-id="98594-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="98594-172">İşlenmiş çıktıyı doğrudan yanıta akıp.</span><span class="sxs-lookup"><span data-stu-id="98594-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="98594-173">Yöntem bir sonuç döndürmediğinden, bir Razor kod bloğu içinde çağrılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="98594-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="98594-174">`RenderPartialAsync`, İçeriği oluşturduğundan, bazı senaryolarda daha iyi performans sağlar.</span><span class="sxs-lookup"><span data-stu-id="98594-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="98594-175">Performans açısından kritik durumlarda, her iki yaklaşımı kullanarak sayfayı kıyaslar ve daha hızlı bir yanıt üreten yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="98594-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="98594-176">Zaman uyumlu HTML Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="98594-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="98594-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> ve <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sırasıyla zaman uyumlu eşdeğerlerdir `PartialAsync` `RenderPartialAsync` .</span><span class="sxs-lookup"><span data-stu-id="98594-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="98594-178">Zaman uyumlu eşdeğerleri, kilitlendikleri senaryolar olduğu için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="98594-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="98594-179">Zaman uyumlu yöntemler gelecek sürümlerde kaldırılmak üzere hedeflenmiştir.</span><span class="sxs-lookup"><span data-stu-id="98594-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98594-180">Kodu yürütmeniz gerekiyorsa, kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components) kullanın.</span><span class="sxs-lookup"><span data-stu-id="98594-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-181">`Partial` `RenderPartial` Bir Visual Studio Çözümleyicisi uyarısıyla çağırma veya sonuç.</span><span class="sxs-lookup"><span data-stu-id="98594-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="98594-182">Örneğin, varlığı `Partial` aşağıdaki uyarı iletisini verir:</span><span class="sxs-lookup"><span data-stu-id="98594-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="98594-183">Ihtmlhelper. Partial kullanımı uygulama kilitlenmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="98594-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="98594-184">&lt;Kısmi &gt; etiket Yardımcısı veya ıhtmlhelper. Partıalasync kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="98594-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="98594-185">Çağrıları `@Html.Partial` ile `@await Html.PartialAsync` veya [kısmi etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="98594-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="98594-186">Kısmi etiket Yardımcısı geçişi hakkında daha fazla bilgi için bkz. [HTML Yardımcısı 'Ndan geçiş](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="98594-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="98594-187">Kısmi görünüm bulma</span><span class="sxs-lookup"><span data-stu-id="98594-187">Partial view discovery</span></span>

<span data-ttu-id="98594-188">Bir dosya uzantısı olmayan kısmi bir görünüme ad ile başvurulduğunda, aşağıdaki konumlar belirtilen sırada aranır:</span><span class="sxs-lookup"><span data-stu-id="98594-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-189">**Razor Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="98594-189">**Razor Pages**</span></span>

1. <span data-ttu-id="98594-190">Şu anda sayfanın klasörü yürütülüyor</span><span class="sxs-lookup"><span data-stu-id="98594-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="98594-191">Sayfanın klasörünün üzerindeki Dizin grafiği</span><span class="sxs-lookup"><span data-stu-id="98594-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="98594-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="98594-192">**MVC**</span></span>

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

<span data-ttu-id="98594-193">Kısmi görünüm bulma için aşağıdaki kurallar geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="98594-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="98594-194">Kısmi görünümler farklı klasörlerde olduğunda aynı dosya adına sahip farklı kısmi görünümlere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="98594-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="98594-195">Dosya uzantısı olmadan kısmi bir görünüme ada göre başvurulması ve kısmi görünümün hem arayanın klasöründe hem de *paylaşılan* klasörde mevcut olması halinde, çağıranın klasöründeki kısmi görünüm kısmi görünümü sağlar.</span><span class="sxs-lookup"><span data-stu-id="98594-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="98594-196">Kısmi görünüm çağıranın klasöründe yoksa, kısmi görünüm *paylaşılan* klasörden sağlanır.</span><span class="sxs-lookup"><span data-stu-id="98594-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="98594-197">*Paylaşılan* klasördeki kısmi görünümler, *paylaşılan kısmi görünümler* veya *varsayılan kısmi görünümler* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="98594-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="98594-198">Kısmi Görünümler *zincirleme* olabilir kısmi &mdash; Görünüm, çağrılar tarafından bir döngüsel başvuru oluşturulmadığı durumlarda başka bir kısmi görünümü çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="98594-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="98594-199">Göreli yollar her zaman geçerli dosyaya göredir, dosyanın köküne veya üst öğesine göre değil.</span><span class="sxs-lookup"><span data-stu-id="98594-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="98594-200">[Razor](xref:mvc/views/razor) `section` Kısmi görünümde tanımlanan bir öğe, üst biçimlendirme dosyaları için görünmez değildir.</span><span class="sxs-lookup"><span data-stu-id="98594-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="98594-201">`section`Yalnızca tanımlandığı kısmi görünüm için görülebilir.</span><span class="sxs-lookup"><span data-stu-id="98594-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="98594-202">Kısmi görünümlerde verilere erişin</span><span class="sxs-lookup"><span data-stu-id="98594-202">Access data from partial views</span></span>

<span data-ttu-id="98594-203">Kısmi bir görünüm örneği oluşturulduğunda, üst öğenin sözlüğünün bir *kopyasını* alır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="98594-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="98594-204">Kısmi görünüm içindeki verilerde yapılan güncelleştirmeler üst görünümde kalıcı değildir.</span><span class="sxs-lookup"><span data-stu-id="98594-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="98594-205">`ViewData` kısmi görünüm geri döndüğünde kısmi görünümdeki değişiklikler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="98594-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="98594-206">Aşağıdaki örnek, bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) örneğinin kısmi bir görünüme nasıl geçirileceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="98594-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="98594-207">Bir modeli kısmi bir görünüme geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="98594-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="98594-208">Model özel bir nesne olabilir.</span><span class="sxs-lookup"><span data-stu-id="98594-208">The model can be a custom object.</span></span> <span data-ttu-id="98594-209">Bir modeli ile geçirebilirsiniz `PartialAsync` (bir içerik bloğunu çağırana kaydedebilir) veya `RenderPartialAsync` (içeriği çıkışa akıp):</span><span class="sxs-lookup"><span data-stu-id="98594-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98594-210">**Razor Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="98594-210">**Razor Pages**</span></span>

<span data-ttu-id="98594-211">Örnek uygulamada aşağıdaki biçimlendirme, *Pages/ArticlesRP/ReadRP. cshtml* sayfasından yapılır.</span><span class="sxs-lookup"><span data-stu-id="98594-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="98594-212">Sayfada iki kısmi görünüm bulunur.</span><span class="sxs-lookup"><span data-stu-id="98594-212">The page contains two partial views.</span></span> <span data-ttu-id="98594-213">İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer.</span><span class="sxs-lookup"><span data-stu-id="98594-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="98594-214">`ViewDataDictionary`Oluşturucu aşırı yüklemesi, `ViewData` var olan sözlüğü korurken yeni bir sözlüğü geçirmek için kullanılır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="98594-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="98594-215">*Pages/Shared/_AuthorPartialRP. cshtml* , *readrp. cshtml* işaretleme dosyası tarafından başvurulan ilk kısmi görünümüdür:</span><span class="sxs-lookup"><span data-stu-id="98594-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="98594-216">*Pages/ArticlesRP/_ArticleSectionRP. cshtml* , *readrp. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümüdür:</span><span class="sxs-lookup"><span data-stu-id="98594-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="98594-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="98594-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="98594-218">Örnek uygulamada aşağıdaki biçimlendirme *görünümleri/makaleleri/Read. cshtml* görünümünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="98594-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="98594-219">Görünüm iki kısmi görünüm içerir.</span><span class="sxs-lookup"><span data-stu-id="98594-219">The view contains two partial views.</span></span> <span data-ttu-id="98594-220">İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer.</span><span class="sxs-lookup"><span data-stu-id="98594-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="98594-221">`ViewDataDictionary`Oluşturucu aşırı yüklemesi, `ViewData` var olan sözlüğü korurken yeni bir sözlüğü geçirmek için kullanılır `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="98594-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="98594-222">*Views/Shared/_AuthorPartial. cshtml* , *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ilk kısmi görünümdür:</span><span class="sxs-lookup"><span data-stu-id="98594-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="98594-223">*Görünümler/makaleler/_ArticleSection. cshtml* *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümdür:</span><span class="sxs-lookup"><span data-stu-id="98594-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="98594-224">Çalışma zamanında, partiler, kendisini paylaşılan *_Layout. cshtml* içinde işlenen üst biçimlendirme dosyasının işlenmiş çıktısına işlenir.</span><span class="sxs-lookup"><span data-stu-id="98594-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="98594-225">İlk kısmi görünüm, makalenin adını ve yayımlama tarihini işler:</span><span class="sxs-lookup"><span data-stu-id="98594-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="98594-226">Abrayhelincoln</span><span class="sxs-lookup"><span data-stu-id="98594-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="98594-227">&lt;Paylaşılan kısmi görünüm dosyası yolundan bu kısmi görünüm &gt; .</span><span class="sxs-lookup"><span data-stu-id="98594-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="98594-228">11/19/1863 12:00:00</span><span class="sxs-lookup"><span data-stu-id="98594-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="98594-229">İkinci kısmi görünüm, makalenin bölümlerini işler:</span><span class="sxs-lookup"><span data-stu-id="98594-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="98594-230">Bölüm bir dizin: 0</span><span class="sxs-lookup"><span data-stu-id="98594-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="98594-231">Dört puan ve yedi yıl önce...</span><span class="sxs-lookup"><span data-stu-id="98594-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="98594-232">Bölüm Iki Dizin: 1</span><span class="sxs-lookup"><span data-stu-id="98594-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="98594-233">Artık harika bir hukuki War, test ediyor...</span><span class="sxs-lookup"><span data-stu-id="98594-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="98594-234">Bölüm üç Dizin: 2</span><span class="sxs-lookup"><span data-stu-id="98594-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="98594-235">Ancak, daha büyük bir fikir için ayıramıyoruz...</span><span class="sxs-lookup"><span data-stu-id="98594-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98594-236">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="98594-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="98594-237">Razor ASP.NET Core için sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="98594-237">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* [<span data-ttu-id="98594-238">Razor ASP.NET Core için sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="98594-238">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
