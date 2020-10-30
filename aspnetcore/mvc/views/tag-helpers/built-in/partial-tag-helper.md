---
title: ASP.NET Core kısmi etiket Yardımcısı
author: scottaddie
description: Kısmi bir görünüm işlenirken ASP.NET Core kısmi etiket yardımcısını ve onun özniteliklerinin her birinin rolünü bulur.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061437"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="14c35-103">ASP.NET Core kısmi etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="14c35-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="14c35-104">[Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="14c35-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="14c35-105">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="14c35-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="14c35-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14c35-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="14c35-107">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="14c35-107">Overview</span></span>

<span data-ttu-id="14c35-108">Kısmi etiket Yardımcısı, sayfalarda ve MVC uygulamalarında kısmi bir [Görünüm](xref:mvc/views/partial) oluşturmak için kullanılır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="14c35-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="14c35-109">Bunu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="14c35-109">Consider that it:</span></span>

* <span data-ttu-id="14c35-110">ASP.NET Core 2,1 veya üstünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="14c35-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="14c35-111">, [HTML yardımcı söz dizimine](xref:mvc/views/partial#reference-a-partial-view)bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="14c35-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="14c35-112">Kısmi görünümü zaman uyumsuz olarak işler.</span><span class="sxs-lookup"><span data-stu-id="14c35-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="14c35-113">Kısmi görünümü işlemeye yönelik HTML yardımcı seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="14c35-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="14c35-114">*Ürün* modeli bu belgenin tamamında örneklerde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="14c35-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="14c35-115">Kısmi etiket Yardımcısı özniteliklerinin bir stoku aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="14c35-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="14c35-116">name</span><span class="sxs-lookup"><span data-stu-id="14c35-116">name</span></span>

<span data-ttu-id="14c35-117">`name`Özniteliği gereklidir.</span><span class="sxs-lookup"><span data-stu-id="14c35-117">The `name` attribute is required.</span></span> <span data-ttu-id="14c35-118">İşlenecek kısmi görünümün adını veya yolunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="14c35-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="14c35-119">Kısmi bir görünüm adı sağlandığında, [görünüm bulma](xref:mvc/views/overview#view-discovery) işlemi başlatılır.</span><span class="sxs-lookup"><span data-stu-id="14c35-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="14c35-120">Açık bir yol sağlandığında bu işlem atlanır.</span><span class="sxs-lookup"><span data-stu-id="14c35-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="14c35-121">Tüm kabul edilebilir `name` değerler için bkz. [kısmi görünüm bulma](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="14c35-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="14c35-122">Aşağıdaki biçimlendirme, *_ProductPartial. cshtml* 'nin *paylaşılan* klasörden yükleneceğini belirten açık bir yol kullanır.</span><span class="sxs-lookup"><span data-stu-id="14c35-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="14c35-123">[For](#for) özniteliği kullanılarak, bağlama için kısmi görünüme bir model geçirilir.</span><span class="sxs-lookup"><span data-stu-id="14c35-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="14c35-124">:</span><span class="sxs-lookup"><span data-stu-id="14c35-124">for</span></span>

<span data-ttu-id="14c35-125">`for`Özniteliği, geçerli modele göre değerlendirilecek bir [modelexpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) atar.</span><span class="sxs-lookup"><span data-stu-id="14c35-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="14c35-126">`ModelExpression`Sözdizimi, sözdizimini anlar `@Model.` .</span><span class="sxs-lookup"><span data-stu-id="14c35-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="14c35-127">Örneğin, `for="Product"` yerine kullanılabilir `for="@Model.Product"` .</span><span class="sxs-lookup"><span data-stu-id="14c35-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="14c35-128">Bu varsayılan çıkarım davranışı, `@` bir satır içi ifade tanımlamak için simgesi kullanılarak geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="14c35-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="14c35-129">Aşağıdaki biçimlendirme *_ProductPartial. cshtml* 'yi yükler:</span><span class="sxs-lookup"><span data-stu-id="14c35-129">The following markup loads *_ProductPartial.cshtml* :</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="14c35-130">Kısmi görünüm, ilişkili sayfa modelinin `Product` özelliğine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="14c35-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="14c35-131">model</span><span class="sxs-lookup"><span data-stu-id="14c35-131">model</span></span>

<span data-ttu-id="14c35-132">`model`Özniteliği kısmi görünüme geçirilecek bir model örneği atar.</span><span class="sxs-lookup"><span data-stu-id="14c35-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="14c35-133">`model`Özniteliği [for](#for) özniteliğiyle birlikte kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="14c35-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="14c35-134">Aşağıdaki biçimlendirmede, yeni bir `Product` nesne örneği oluşturulur ve `model` bağlama için özniteliğine geçirilir:</span><span class="sxs-lookup"><span data-stu-id="14c35-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="14c35-135">verileri görüntüle</span><span class="sxs-lookup"><span data-stu-id="14c35-135">view-data</span></span>

<span data-ttu-id="14c35-136">`view-data`Özniteliği, kısmi görünüme geçirilecek bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) atar.</span><span class="sxs-lookup"><span data-stu-id="14c35-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="14c35-137">Aşağıdaki biçimlendirme tüm ViewData toplamasını kısmi görünüm için erişilebilir hale getirir:</span><span class="sxs-lookup"><span data-stu-id="14c35-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="14c35-138">Yukarıdaki kodda, `IsNumberReadOnly` anahtar değeri olarak ayarlanır `true` ve ViewData koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="14c35-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="14c35-139">Sonuç olarak, `ViewData["IsNumberReadOnly"]` aşağıdaki kısmi görünüm içinde erişilebilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="14c35-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="14c35-140">Bu örnekte, değeri, `ViewData["IsNumberReadOnly"]` *sayı* alanının salt okunurdur olarak görüntülenip görüntülenmeyeceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="14c35-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="14c35-141">HTML yardımcısından geçiş yapma</span><span class="sxs-lookup"><span data-stu-id="14c35-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="14c35-142">Aşağıdaki zaman uyumsuz HTML Yardımcısı örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="14c35-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="14c35-143">Bir ürün koleksiyonu tekrarlandırılır ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="14c35-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="14c35-144">`PartialAsync`Metodun ilk parametresine göre *_ProductPartial. cshtml* kısmi görünümü yüklenir.</span><span class="sxs-lookup"><span data-stu-id="14c35-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="14c35-145">Modelin bir örneği `Product` bağlama için kısmi görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="14c35-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="14c35-146">Aşağıdaki kısmi etiket Yardımcısı HTML Yardımcısı ile aynı zaman uyumsuz işleme davranışına erişir `PartialAsync` .</span><span class="sxs-lookup"><span data-stu-id="14c35-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="14c35-147">`model`Özniteliğe `Product` kısmi görünüme bağlama için bir model örneği atanır.</span><span class="sxs-lookup"><span data-stu-id="14c35-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="14c35-148">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14c35-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
