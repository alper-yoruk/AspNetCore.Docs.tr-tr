---
title: ASP.NET Core 'de betik etiketi Yardımcısı
author: rick-anderson
ms.author: riande
description: ASP.NET Core betik etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML komut dosyası etiketinin genişletme davranışında oynadığı rolü bulur.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- 'appsettings.json'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053507"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="ec2fb-103">ASP.NET Core 'de betik etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="ec2fb-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ec2fb-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec2fb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ec2fb-105">[Betik etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) , birincil veya geri dönüş betik dosyasına bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="ec2fb-106">Genellikle birincil betik dosyası [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="ec2fb-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="ec2fb-107">Betik etiketi Yardımcısı, CDN kullanılabilir olmadığında betik dosyası ve geri dönüş için CDN belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="ec2fb-108">Betik etiketi Yardımcısı, bir CDN 'nin performans avantajlarından yararlanarak yerel barındırma sağlamlığı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="ec2fb-109">Aşağıdaki Razor biçimlendirmede `script` geri dönüş içeren bir öğe gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ec2fb-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="ec2fb-110">`<script>`CDN betiğini yüklemeyi erteleme için öğenin [erteleme](https://developer.mozilla.org/docs/Web/HTML/Element/script) özniteliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="ec2fb-111">Komut dosyası etiketi Yardımcısı, [ASP-Fallback-test](#asp-fallback-test) ifadesini hemen yürüten JavaScript 'i işler.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="ec2fb-112">CDN betiği yükleme ertelenir ise ifade başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="ec2fb-113">Yaygın olarak kullanılan betik etiketi Yardımcısı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="ec2fb-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="ec2fb-114">Tüm betik etiketi Yardımcısı öznitelikleri, özellikleri ve yöntemleri için [komut dosyası etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) ' na bakın.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="ec2fb-115">ASP-geri dönüş-test</span><span class="sxs-lookup"><span data-stu-id="ec2fb-115">asp-fallback-test</span></span>

<span data-ttu-id="ec2fb-116">Geri dönüş testi için kullanılacak birincil betikte tanımlanan betik yöntemi.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="ec2fb-117">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="ec2fb-118">ASP-geri dönüş-src</span><span class="sxs-lookup"><span data-stu-id="ec2fb-118">asp-fallback-src</span></span>

<span data-ttu-id="ec2fb-119">Birincil bir hata durumunda öğesine geri dönüş yapılacak bir betik etiketinin URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="ec2fb-120">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="ec2fb-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec2fb-121">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ec2fb-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
