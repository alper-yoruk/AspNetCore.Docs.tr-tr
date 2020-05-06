---
title: ASP.NET Core 'de bağlayıcı etiketi Yardımcısı
author: pkellner
description: ASP.NET Core tutturucu etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML bağlantısı etiketinin genişletme davranışında oynadığı rolü bulur.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 142ad62bbbc25fc5390331b253a6173f064ef162
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773971"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="d86a2-103">ASP.NET Core 'de bağlayıcı etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="d86a2-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d86a2-104">By [Peter Kellner](https://peterkellner.net) ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d86a2-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d86a2-105">[Tutturucu etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) , yeni öznitelikler ekleyerek standart HTML Tutturucu`<a ... ></a>`() etiketini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="d86a2-106">Kurala göre, öznitelik adları ön ekine sahiptir `asp-`.</span><span class="sxs-lookup"><span data-stu-id="d86a2-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="d86a2-107">İşlenmiş tutturucu öğenin `href` öznitelik değeri, `asp-` özniteliklerin değerlerine göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="d86a2-108">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro>..</span><span class="sxs-lookup"><span data-stu-id="d86a2-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="d86a2-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d86a2-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d86a2-110">*Speakercontroller* bu belgenin tamamında örneklerde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d86a2-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="d86a2-111">Tutturucu etiketi yardımcı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="d86a2-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="d86a2-112">ASP-Controller</span><span class="sxs-lookup"><span data-stu-id="d86a2-112">asp-controller</span></span>

<span data-ttu-id="d86a2-113">[ASP-Controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) ÖZNITELIĞI, URL oluşturmak için kullanılan denetleyiciyi atar.</span><span class="sxs-lookup"><span data-stu-id="d86a2-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="d86a2-114">Aşağıdaki biçimlendirme tüm hoparlörleri listeler:</span><span class="sxs-lookup"><span data-stu-id="d86a2-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="d86a2-115">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="d86a2-116">`asp-controller` Özniteliği belirtilmişse ve `asp-action` yoksa, varsayılan `asp-action` değer şu anda yürütülmekte olan görünümle ilişkili denetleyici eylemi olur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="d86a2-117">`asp-action` Önceki biçimlendirmeden atlanırsa ve bağlayıcı etiketi Yardımcısı *HomeController*'ın *Dizin* görünümünde (*/Home*) kullanılırsa, oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="d86a2-118">ASP-eylem</span><span class="sxs-lookup"><span data-stu-id="d86a2-118">asp-action</span></span>

<span data-ttu-id="d86a2-119">[ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) öznitelik değeri, oluşturulan `href` özniteliğe dahil olan denetleyici eylemi adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d86a2-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="d86a2-120">Aşağıdaki biçimlendirme oluşturulan `href` öznitelik değerini konuşmacı değerlendirmeleri sayfasına ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d86a2-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="d86a2-121">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="d86a2-122">Hiçbir `asp-controller` öznitelik belirtilmemişse, geçerli görünümü yürüten görünümü çağıran varsayılan denetleyici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="d86a2-123">`asp-action` Öznitelik değeri Ise `Index`, URL 'ye hiçbir eylem eklenmez ve bu, varsayılan `Index` eylemi çağırmaya önde gelen bir eylem değildir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="d86a2-124">Belirtilen eylem (veya varsayılan olarak), içinde `asp-controller`başvurulan denetleyicide bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="d86a2-125">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="d86a2-125">asp-route-{value}</span></span>

<span data-ttu-id="d86a2-126">[ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği bir joker karakter yolu öneki sunar.</span><span class="sxs-lookup"><span data-stu-id="d86a2-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="d86a2-127">`{value}` Yer tutucusunu kaplayan herhangi bir değer olası bir yol parametresi olarak yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="d86a2-128">Varsayılan bir yol bulunmazsa, bu rota öneki oluşturulan `href` özniteliğe bir istek parametresi ve değeri olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="d86a2-129">Aksi takdirde, yol şablonunda değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="d86a2-130">Aşağıdaki denetleyici eylemini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d86a2-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="d86a2-131">Başlangıçta tanımlanmış varsayılan bir yol şablonuyla *. Yapılandır*:</span><span class="sxs-lookup"><span data-stu-id="d86a2-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="d86a2-132">MVC görünümü, aşağıdaki gibi eylem tarafından belirtilen modeli kullanır:</span><span class="sxs-lookup"><span data-stu-id="d86a2-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="d86a2-133">Varsayılan yolun `{id?}` yer tutucusu eşleşti.</span><span class="sxs-lookup"><span data-stu-id="d86a2-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="d86a2-134">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="d86a2-135">Aşağıdaki MVC görünümünde olduğu gibi, yol ön ekinin eşleşen yönlendirme şablonunun bir parçası olmadığını varsayın:</span><span class="sxs-lookup"><span data-stu-id="d86a2-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="d86a2-136">Eşleşen rotada bulunamadığı için aşağıdaki HTML `speakerid` oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="d86a2-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="d86a2-137">Ya da `asp-controller` `asp-action` belirtilmemişse, aynı varsayılan işleme, `asp-route` özniteliğinde olduğu gibi olur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="d86a2-138">ASP-Route</span><span class="sxs-lookup"><span data-stu-id="d86a2-138">asp-route</span></span>

<span data-ttu-id="d86a2-139">[ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) özniteliği, doğrudan adlandırılmış bir yola bağlanan bir URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="d86a2-140">[Yönlendirme özniteliklerini](xref:mvc/controllers/routing#attribute-routing)kullanarak, bir yol, `SpeakerController` öğesinde gösterildiği gibi adlandırılabilir ve `Evaluations` eyleminde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d86a2-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="d86a2-141">Aşağıdaki biçimlendirmede `asp-route` öznitelik, adlandırılmış yola başvurur:</span><span class="sxs-lookup"><span data-stu-id="d86a2-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="d86a2-142">Tutturucu etiketi Yardımcısı, */Hoparlörker/değerlendirmeleri*URL 'sini kullanarak bu denetleyiciye doğrudan bir yol oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="d86a2-143">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="d86a2-144">`asp-controller` Veya `asp-action` buna ek olarak belirtilmişse `asp-route`, oluşturulan yol beklediğiniz gibi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="d86a2-145">Bir yol çakışmasını `asp-route` önlemek için `asp-controller` ve `asp-action` öznitelikleriyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="d86a2-146">ASP-All-Route-Data</span><span class="sxs-lookup"><span data-stu-id="d86a2-146">asp-all-route-data</span></span>

<span data-ttu-id="d86a2-147">[ASP-All-Route-Data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği, anahtar-değer çiftleri sözlüğü oluşturmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d86a2-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="d86a2-148">Anahtar parametre adıdır ve değer parametre değeridir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="d86a2-149">Aşağıdaki örnekte, bir sözlük başlatılır ve bir Razor görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="d86a2-150">Alternatif olarak, veriler modelinize iletilebilir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="d86a2-151">Yukarıdaki kod, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d86a2-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="d86a2-152">`asp-all-route-data` Sözlük, aşırı yüklenmiş `Evaluations` eylemin gereksinimlerini karşılayan bir QueryString oluşturmak için düzleştirilir:</span><span class="sxs-lookup"><span data-stu-id="d86a2-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="d86a2-153">Sözlükteki herhangi bir anahtar yol parametreleriyle eşleşiyorsa, bu değerler rotada uygun şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="d86a2-154">Diğer eşleşme olmayan değerler istek parametreleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="d86a2-155">ASP-Fragment</span><span class="sxs-lookup"><span data-stu-id="d86a2-155">asp-fragment</span></span>

<span data-ttu-id="d86a2-156">[ASP-Fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) özniteliği URL 'ye eklenecek URL parçasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d86a2-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="d86a2-157">Tutturucu etiketi Yardımcısı, karma karakterini (#) ekler.</span><span class="sxs-lookup"><span data-stu-id="d86a2-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="d86a2-158">Aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d86a2-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="d86a2-159">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="d86a2-160">Karma Etiketler, istemci tarafı uygulamalar oluşturulurken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="d86a2-161">JavaScript 'te kolay işaretlemek ve aramak için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="d86a2-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="d86a2-162">ASP-alanı</span><span class="sxs-lookup"><span data-stu-id="d86a2-162">asp-area</span></span>

<span data-ttu-id="d86a2-163">[ASP-Area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) özniteliği, uygun yolu ayarlamak için kullanılan alan adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d86a2-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="d86a2-164">Aşağıdaki örneklerde, `asp-area` özniteliğinin yolların yeniden eşleştirmesine neden olduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="d86a2-165">Razor Sayfalarda kullanım</span><span class="sxs-lookup"><span data-stu-id="d86a2-165">Usage in Razor Pages</span></span>

Razor<span data-ttu-id="d86a2-166">Sayfa alanı ASP.NET Core 2,1 veya üzeri sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-166"> Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="d86a2-167">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d86a2-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="d86a2-168">**{Proje adı}**</span><span class="sxs-lookup"><span data-stu-id="d86a2-168">**{Project name}**</span></span>
  * <span data-ttu-id="d86a2-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="d86a2-169">**wwwroot**</span></span>
  * <span data-ttu-id="d86a2-170">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-170">**Areas**</span></span>
    * <span data-ttu-id="d86a2-171">**Oturumlar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-171">**Sessions**</span></span>
      * <span data-ttu-id="d86a2-172">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-172">**Pages**</span></span>
        * <span data-ttu-id="d86a2-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d86a2-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="d86a2-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d86a2-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="d86a2-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d86a2-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="d86a2-176">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-176">**Pages**</span></span>

<span data-ttu-id="d86a2-177">*Oturumlar* alanı *Dizin* Razor sayfasına başvurmak için yapılan biçimlendirme şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d86a2-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="d86a2-178">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="d86a2-179">Bir Razor sayfalar uygulamasındaki bölgeleri desteklemek için aşağıdakilerden birini yapın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d86a2-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="d86a2-180">[Uyumluluk sürümünü](xref:mvc/compatibility-version) 2,1 veya üzeri olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d86a2-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="d86a2-181">[RazorPagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) özelliğini şu şekilde `true`ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="d86a2-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="d86a2-182">MVC 'de kullanım</span><span class="sxs-lookup"><span data-stu-id="d86a2-182">Usage in MVC</span></span>

<span data-ttu-id="d86a2-183">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d86a2-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="d86a2-184">**{Proje adı}**</span><span class="sxs-lookup"><span data-stu-id="d86a2-184">**{Project name}**</span></span>
  * <span data-ttu-id="d86a2-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="d86a2-185">**wwwroot**</span></span>
  * <span data-ttu-id="d86a2-186">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-186">**Areas**</span></span>
    * <span data-ttu-id="d86a2-187">**Bloglar**</span><span class="sxs-lookup"><span data-stu-id="d86a2-187">**Blogs**</span></span>
      * <span data-ttu-id="d86a2-188">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="d86a2-188">**Controllers**</span></span>
        * <span data-ttu-id="d86a2-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="d86a2-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="d86a2-190">**Görünümler**</span><span class="sxs-lookup"><span data-stu-id="d86a2-190">**Views**</span></span>
        * <span data-ttu-id="d86a2-191">**Giriş**</span><span class="sxs-lookup"><span data-stu-id="d86a2-191">**Home**</span></span>
          * <span data-ttu-id="d86a2-192">*AboutBlog. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d86a2-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="d86a2-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d86a2-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="d86a2-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d86a2-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="d86a2-195">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="d86a2-195">**Controllers**</span></span>

<span data-ttu-id="d86a2-196">" `asp-area` Bloglar" ayarı, Dizin *alanları/bloglarını* bu tutturucu etiketi için ilişkili denetleyicilerin ve görünümlerin yollarına ön ekler.</span><span class="sxs-lookup"><span data-stu-id="d86a2-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="d86a2-197">*Aboutblog* görünümüne başvurmak için yapılan biçimlendirme şu şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="d86a2-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="d86a2-198">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="d86a2-199">MVC uygulamasındaki bölgeleri desteklemek için, yol şablonu varsa alana bir başvuru içermelidir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="d86a2-200">Bu şablon, başlangıç sırasında `routes.MapRoute` yöntem çağrısının ikinci parametresiyle temsil edilir *. configure*:</span><span class="sxs-lookup"><span data-stu-id="d86a2-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="d86a2-201">ASP-protokol</span><span class="sxs-lookup"><span data-stu-id="d86a2-201">asp-protocol</span></span>

<span data-ttu-id="d86a2-202">[ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) ÖZNITELIĞI, URL 'niz için bir protokol (gibi `https`) belirtmek içindir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="d86a2-203">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d86a2-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="d86a2-204">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="d86a2-205">Örnekteki ana bilgisayar adı localhost 'tur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-205">The host name in the example is localhost.</span></span> <span data-ttu-id="d86a2-206">Tutturucu etiketi Yardımcısı, URL oluştururken Web sitesinin ortak etki alanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="d86a2-207">ASP-ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="d86a2-207">asp-host</span></span>

<span data-ttu-id="d86a2-208">[ASP-Host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) ÖZNITELIĞI, URL 'niz için bir ana bilgisayar adı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="d86a2-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="d86a2-209">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d86a2-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="d86a2-210">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="d86a2-211">asp-sayfa</span><span class="sxs-lookup"><span data-stu-id="d86a2-211">asp-page</span></span>

<span data-ttu-id="d86a2-212">[ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) özniteliği Razor sayfalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="d86a2-213">Bir tutturucu etiketinin `href` öznitelik değerini belirli bir sayfaya ayarlamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d86a2-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="d86a2-214">Sayfa adının eğik çizgiyle ("/") önek olarak URL 'SI oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d86a2-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="d86a2-215">Aşağıdaki örnek, katılımcı Razor sayfasına işaret eder:</span><span class="sxs-lookup"><span data-stu-id="d86a2-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="d86a2-216">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="d86a2-217">`asp-page` Özniteliği `asp-route`, `asp-controller`, ve `asp-action` öznitelikleriyle birbirini dışlıyor.</span><span class="sxs-lookup"><span data-stu-id="d86a2-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="d86a2-218">Ancak, `asp-page` aşağıdaki işaretlemenin gösterdiği `asp-route-{value}` gibi yönlendirmeyi denetlemek için ile birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d86a2-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="d86a2-219">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="d86a2-220">ASP-Page-Handler</span><span class="sxs-lookup"><span data-stu-id="d86a2-220">asp-page-handler</span></span>

<span data-ttu-id="d86a2-221">[ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) özniteliği Razor sayfalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="d86a2-222">Belirli sayfa işleyicileriyle bağlantı için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="d86a2-223">Aşağıdaki sayfa işleyicisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d86a2-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="d86a2-224">Sayfa modelinin ilişkili biçimlendirmesi `OnGetProfile` sayfa işleyicisine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="d86a2-225">Göz önünde `On<Verb>` , sayfa işleyicisi yöntem adının ön eki `asp-page-handler` öznitelik değerinde atlanacaktır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="d86a2-226">Yöntem zaman uyumsuz olduğunda, `Async` sonek de atlanır.</span><span class="sxs-lookup"><span data-stu-id="d86a2-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="d86a2-227">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="d86a2-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="d86a2-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d86a2-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
