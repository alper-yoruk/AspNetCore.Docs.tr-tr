---
title: ASP.NET Core etiket Yardımcısı bağlantı
author: rick-anderson
ms.author: riande
description: ASP.NET Core link etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML bağlantısı etiketinin genişletme davranışında oynadığı rolü bulur.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: e1e2e58b4ab9087e1f9de5b5c03b587feb88f1b9
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256521"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="914e1-103">ASP.NET Core etiket Yardımcısı bağlantı</span><span class="sxs-lookup"><span data-stu-id="914e1-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="914e1-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="914e1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="914e1-105">[Bağlantı etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) , birincil veya GERI dönüş CSS dosyasına bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="914e1-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="914e1-106">Genellikle birincil CSS dosyası [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="914e1-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="914e1-107">Bağlantı etiketi Yardımcısı, CSS dosyası için CDN ve CDN kullanılabilir olmadığında geri dönüş belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="914e1-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="914e1-108">Bağlantı etiketi Yardımcısı, CDN 'nin performans avantajlarından yararlanarak yerel barındırma sağlamlığı sağlar.</span><span class="sxs-lookup"><span data-stu-id="914e1-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="914e1-109">Aşağıdaki Razor biçimlendirmesinde, ASP.NET Core Web `head` uygulaması şablonuyla oluşturulan bir düzen dosyasının öğesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="914e1-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="914e1-110">Aşağıdaki kod, önceki koddan (geliştirme olmayan bir ortamda) HTML olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="914e1-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="914e1-111">Önceki kodda, bağlantı etiketi Yardımcısı `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` öğesi ve istenen *Bootstrap. min. css* dosyasının CDN üzerinde kullanılabilir olduğunu doğrulamak için kullanılan aşağıdaki JavaScript 'i oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="914e1-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="914e1-112">Bu durumda, CSS dosyası kullanılabilir olduğundan, etiket Yardımcısı CDN CSS dosyası ile `<link />` öğeyi üretti.</span><span class="sxs-lookup"><span data-stu-id="914e1-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="914e1-113">Yaygın olarak kullanılan bağlantı etiketi Yardımcısı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="914e1-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="914e1-114">Tüm bağlantı etiketi Yardımcısı öznitelikleri, özellikleri ve yöntemleri için [bağlantı etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) ' na bakın.</span><span class="sxs-lookup"><span data-stu-id="914e1-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="914e1-115">değerini</span><span class="sxs-lookup"><span data-stu-id="914e1-115">href</span></span>

<span data-ttu-id="914e1-116">Bağlı kaynağın tercih edilen adresi.</span><span class="sxs-lookup"><span data-stu-id="914e1-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="914e1-117">Adres, her durumda oluşturulan HTML 'ye düşünce olarak iletilir.</span><span class="sxs-lookup"><span data-stu-id="914e1-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="914e1-118">ASP-geri dönüş-href</span><span class="sxs-lookup"><span data-stu-id="914e1-118">asp-fallback-href</span></span>

<span data-ttu-id="914e1-119">Birincil URL 'nin başarısız olması durumunda öğesine geri dönüş için CSS stil sayfasının URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="914e1-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="914e1-120">ASP-geri dönüş-test sınıfı</span><span class="sxs-lookup"><span data-stu-id="914e1-120">asp-fallback-test-class</span></span>

<span data-ttu-id="914e1-121">Geri dönüş testi için kullanılacak stil sayfasında tanımlanan sınıf adı.</span><span class="sxs-lookup"><span data-stu-id="914e1-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="914e1-122">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="914e1-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="914e1-123">ASP-Fallback-test-özelliği</span><span class="sxs-lookup"><span data-stu-id="914e1-123">asp-fallback-test-property</span></span>

<span data-ttu-id="914e1-124">Geri dönüş testi için kullanılacak CSS özellik adı.</span><span class="sxs-lookup"><span data-stu-id="914e1-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="914e1-125">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="914e1-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="914e1-126">ASP-geri dönüş-test-değeri</span><span class="sxs-lookup"><span data-stu-id="914e1-126">asp-fallback-test-value</span></span>

<span data-ttu-id="914e1-127">Geri dönüş testi için kullanılacak CSS özelliği değeri.</span><span class="sxs-lookup"><span data-stu-id="914e1-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="914e1-128">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="914e1-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="914e1-129">ASP-geri dönüş-test-değeri</span><span class="sxs-lookup"><span data-stu-id="914e1-129">asp-fallback-test-value</span></span>

<span data-ttu-id="914e1-130">Geri dönüş testi için kullanılacak CSS özelliği değeri.</span><span class="sxs-lookup"><span data-stu-id="914e1-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="914e1-131">Daha fazla bilgi için bkz.<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span><span class="sxs-lookup"><span data-stu-id="914e1-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="914e1-132">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="914e1-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>