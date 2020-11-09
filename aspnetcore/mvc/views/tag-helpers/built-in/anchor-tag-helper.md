---
title: ASP.NET Core 'de bağlayıcı etiketi Yardımcısı
author: pkellner
description: ASP.NET Core tutturucu etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML bağlantısı etiketinin genişletme davranışında oynadığı rolü bulur.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: d39db59b0fc273fe4193a4864f302ecd3f4ad348
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060917"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="50210-103">ASP.NET Core 'de bağlayıcı etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="50210-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="50210-104">By [Peter Kellner](https://peterkellner.net) ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="50210-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="50210-105">[Tutturucu etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) , yeni öznitelikler ekleyerek standart HTML Tutturucu ( `<a ... ></a>` ) etiketini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="50210-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="50210-106">Kurala göre, öznitelik adları ön ekine sahiptir `asp-` .</span><span class="sxs-lookup"><span data-stu-id="50210-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="50210-107">İşlenmiş tutturucu öğenin `href` öznitelik değeri, özniteliklerin değerlerine göre belirlenir `asp-` .</span><span class="sxs-lookup"><span data-stu-id="50210-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="50210-108">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="50210-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="50210-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="50210-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="50210-110">*Speakercontroller* bu belgenin tamamında örneklerde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="50210-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="50210-111">Tutturucu etiketi yardımcı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="50210-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="50210-112">ASP-Controller</span><span class="sxs-lookup"><span data-stu-id="50210-112">asp-controller</span></span>

<span data-ttu-id="50210-113">[ASP-Controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) ÖZNITELIĞI, URL oluşturmak için kullanılan denetleyiciyi atar.</span><span class="sxs-lookup"><span data-stu-id="50210-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="50210-114">Aşağıdaki biçimlendirme tüm hoparlörleri listeler:</span><span class="sxs-lookup"><span data-stu-id="50210-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="50210-115">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="50210-116">`asp-controller`Özniteliği belirtilmişse ve `asp-action` yoksa, varsayılan `asp-action` değer şu anda yürütülmekte olan görünümle ilişkili denetleyici eylemi olur.</span><span class="sxs-lookup"><span data-stu-id="50210-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="50210-117">`asp-action`Önceki biçimlendirmeden atlanırsa ve bağlayıcı etiketi Yardımcısı *HomeController* 'ın *Dizin* görünümünde ( */Home* ) kullanılırsa, oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController* 's *Index* view ( */Home* ), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="50210-118">ASP-eylem</span><span class="sxs-lookup"><span data-stu-id="50210-118">asp-action</span></span>

<span data-ttu-id="50210-119">[ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) öznitelik değeri, oluşturulan özniteliğe dahil olan denetleyici eylemi adını temsil eder `href` .</span><span class="sxs-lookup"><span data-stu-id="50210-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="50210-120">Aşağıdaki biçimlendirme oluşturulan `href` öznitelik değerini konuşmacı değerlendirmeleri sayfasına ayarlar:</span><span class="sxs-lookup"><span data-stu-id="50210-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="50210-121">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="50210-122">Hiçbir `asp-controller` öznitelik belirtilmemişse, geçerli görünümü yürüten görünümü çağıran varsayılan denetleyici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50210-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="50210-123">`asp-action`Öznitelik değeri ise `Index` , URL 'ye hiçbir eylem eklenmez ve bu, varsayılan eylemi çağırmaya önde gelen bir eylem değildir `Index` .</span><span class="sxs-lookup"><span data-stu-id="50210-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="50210-124">Belirtilen eylem (veya varsayılan olarak), içinde başvurulan denetleyicide bulunmalıdır `asp-controller` .</span><span class="sxs-lookup"><span data-stu-id="50210-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="50210-125">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="50210-125">asp-route-{value}</span></span>

<span data-ttu-id="50210-126">[ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği bir joker karakter yolu öneki sunar.</span><span class="sxs-lookup"><span data-stu-id="50210-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="50210-127">Yer tutucusunu kaplayan herhangi bir değer `{value}` olası bir yol parametresi olarak yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="50210-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="50210-128">Varsayılan bir yol bulunmazsa, bu rota öneki oluşturulan `href` özniteliğe bir istek parametresi ve değeri olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="50210-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="50210-129">Aksi takdirde, yol şablonunda değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="50210-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="50210-130">Aşağıdaki denetleyici eylemini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="50210-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="50210-131">*Startup.Configure* içinde tanımlanmış bir varsayılan yol şablonuyla:</span><span class="sxs-lookup"><span data-stu-id="50210-131">With a default route template defined in *Startup.Configure* :</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="50210-132">MVC görünümü, aşağıdaki gibi eylem tarafından belirtilen modeli kullanır:</span><span class="sxs-lookup"><span data-stu-id="50210-132">The MVC view uses the model, provided by the action, as follows:</span></span>

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

<span data-ttu-id="50210-133">Varsayılan yolun `{id?}` yer tutucusu eşleşti.</span><span class="sxs-lookup"><span data-stu-id="50210-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="50210-134">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="50210-135">Aşağıdaki MVC görünümünde olduğu gibi, yol ön ekinin eşleşen yönlendirme şablonunun bir parçası olmadığını varsayın:</span><span class="sxs-lookup"><span data-stu-id="50210-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

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

<span data-ttu-id="50210-136">Eşleşen rotada bulunamadığı için aşağıdaki HTML oluşturuldu `speakerid` :</span><span class="sxs-lookup"><span data-stu-id="50210-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="50210-137">Ya da `asp-controller` `asp-action` belirtilmemişse, aynı varsayılan işleme, özniteliğinde olduğu gibi olur `asp-route` .</span><span class="sxs-lookup"><span data-stu-id="50210-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="50210-138">ASP-Route</span><span class="sxs-lookup"><span data-stu-id="50210-138">asp-route</span></span>

<span data-ttu-id="50210-139">[ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) özniteliği, doğrudan adlandırılmış bir yola bağlanan bir URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50210-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="50210-140">[Yönlendirme özniteliklerini](xref:mvc/controllers/routing#attribute-routing)kullanarak, bir yol, öğesinde gösterildiği gibi adlandırılabilir `SpeakerController` ve `Evaluations` eyleminde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="50210-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="50210-141">Aşağıdaki biçimlendirmede `asp-route` öznitelik, adlandırılmış yola başvurur:</span><span class="sxs-lookup"><span data-stu-id="50210-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="50210-142">Tutturucu etiketi Yardımcısı, */Hoparlörker/değerlendirmeleri* URL 'sini kullanarak bu denetleyiciye doğrudan bir yol oluşturur.</span><span class="sxs-lookup"><span data-stu-id="50210-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations* .</span></span> <span data-ttu-id="50210-143">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="50210-144">`asp-controller`Veya `asp-action` buna ek olarak belirtilmişse `asp-route` , oluşturulan yol beklediğiniz gibi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="50210-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="50210-145">Bir yol çakışmasını önlemek için `asp-route` `asp-controller` ve `asp-action` öznitelikleriyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="50210-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="50210-146">ASP-All-Route-Data</span><span class="sxs-lookup"><span data-stu-id="50210-146">asp-all-route-data</span></span>

<span data-ttu-id="50210-147">[ASP-All-Route-Data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği, anahtar-değer çiftleri sözlüğü oluşturmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="50210-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="50210-148">Anahtar parametre adıdır ve değer parametre değeridir.</span><span class="sxs-lookup"><span data-stu-id="50210-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="50210-149">Aşağıdaki örnekte, bir sözlük başlatılır ve bir Razor görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="50210-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="50210-150">Alternatif olarak, veriler modelinize iletilebilir.</span><span class="sxs-lookup"><span data-stu-id="50210-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="50210-151">Yukarıdaki kod, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="50210-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="50210-152">`asp-all-route-data`Sözlük, aşırı yüklenmiş eylemin gereksinimlerini karşılayan bir QueryString oluşturmak için düzleştirilir `Evaluations` :</span><span class="sxs-lookup"><span data-stu-id="50210-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="50210-153">Sözlükteki herhangi bir anahtar yol parametreleriyle eşleşiyorsa, bu değerler rotada uygun şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="50210-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="50210-154">Diğer eşleşme olmayan değerler istek parametreleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="50210-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="50210-155">ASP-Fragment</span><span class="sxs-lookup"><span data-stu-id="50210-155">asp-fragment</span></span>

<span data-ttu-id="50210-156">[ASP-Fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) özniteliği URL 'ye eklenecek URL parçasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="50210-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="50210-157">Tutturucu etiketi Yardımcısı, karma karakterini (#) ekler.</span><span class="sxs-lookup"><span data-stu-id="50210-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="50210-158">Aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="50210-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="50210-159">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="50210-160">Karma Etiketler, istemci tarafı uygulamalar oluşturulurken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="50210-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="50210-161">JavaScript 'te kolay işaretlemek ve aramak için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="50210-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="50210-162">ASP-alanı</span><span class="sxs-lookup"><span data-stu-id="50210-162">asp-area</span></span>

<span data-ttu-id="50210-163">[ASP-Area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) özniteliği, uygun yolu ayarlamak için kullanılan alan adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="50210-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="50210-164">Aşağıdaki örneklerde, `asp-area` özniteliğinin yolların yeniden eşleştirmesine neden olduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="50210-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-no-locrazor-pages"></a><span data-ttu-id="50210-165">Sayfalarda kullanım Razor</span><span class="sxs-lookup"><span data-stu-id="50210-165">Usage in Razor Pages</span></span>

<span data-ttu-id="50210-166">Razor Sayfa alanı ASP.NET Core 2,1 veya üzeri sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="50210-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="50210-167">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="50210-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="50210-168">**{Proje adı}**</span><span class="sxs-lookup"><span data-stu-id="50210-168">**{Project name}**</span></span>
  * <span data-ttu-id="50210-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="50210-169">**wwwroot**</span></span>
  * <span data-ttu-id="50210-170">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="50210-170">**Areas**</span></span>
    * <span data-ttu-id="50210-171">**Oturumlar**</span><span class="sxs-lookup"><span data-stu-id="50210-171">**Sessions**</span></span>
      * <span data-ttu-id="50210-172">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="50210-172">**Pages**</span></span>
        * <span data-ttu-id="50210-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="50210-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="50210-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="50210-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="50210-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="50210-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="50210-176">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="50210-176">**Pages**</span></span>

<span data-ttu-id="50210-177">*Oturumlar* alanı *Dizin* sayfasına başvurmak için yapılan biçimlendirme şunlardır Razor :</span><span class="sxs-lookup"><span data-stu-id="50210-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="50210-178">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="50210-179">Bir sayfalar uygulamasındaki bölgeleri desteklemek için aşağıdakilerden Razor birini yapın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50210-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="50210-180">[Uyumluluk sürümünü](xref:mvc/compatibility-version) 2,1 veya üzeri olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50210-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="50210-181">[ Razor Pagesoptions. allowareas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) özelliğini şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="50210-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="50210-182">MVC 'de kullanım</span><span class="sxs-lookup"><span data-stu-id="50210-182">Usage in MVC</span></span>

<span data-ttu-id="50210-183">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="50210-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="50210-184">**{Proje adı}**</span><span class="sxs-lookup"><span data-stu-id="50210-184">**{Project name}**</span></span>
  * <span data-ttu-id="50210-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="50210-185">**wwwroot**</span></span>
  * <span data-ttu-id="50210-186">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="50210-186">**Areas**</span></span>
    * <span data-ttu-id="50210-187">**Bloglar**</span><span class="sxs-lookup"><span data-stu-id="50210-187">**Blogs**</span></span>
      * <span data-ttu-id="50210-188">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="50210-188">**Controllers**</span></span>
        * <span data-ttu-id="50210-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="50210-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="50210-190">**Görünümler**</span><span class="sxs-lookup"><span data-stu-id="50210-190">**Views**</span></span>
        * <span data-ttu-id="50210-191">**Giriş Ekranı**</span><span class="sxs-lookup"><span data-stu-id="50210-191">**Home**</span></span>
          * <span data-ttu-id="50210-192">*AboutBlog. cshtml*</span><span class="sxs-lookup"><span data-stu-id="50210-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="50210-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="50210-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="50210-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="50210-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="50210-195">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="50210-195">**Controllers**</span></span>

<span data-ttu-id="50210-196">`asp-area`"Bloglar" ayarı, Dizin *alanları/bloglarını* bu tutturucu etiketi için ilişkili denetleyicilerin ve görünümlerin yollarına ön ekler.</span><span class="sxs-lookup"><span data-stu-id="50210-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="50210-197">*Aboutblog* görünümüne başvurmak için yapılan biçimlendirme şu şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="50210-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="50210-198">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="50210-199">MVC uygulamasındaki bölgeleri desteklemek için, yol şablonu varsa alana bir başvuru içermelidir.</span><span class="sxs-lookup"><span data-stu-id="50210-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="50210-200">Bu şablon `routes.MapRoute` *Startup.Configure* içindeki yöntem çağrısının ikinci parametresiyle temsil edilir:</span><span class="sxs-lookup"><span data-stu-id="50210-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure* :</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="50210-201">ASP-protokol</span><span class="sxs-lookup"><span data-stu-id="50210-201">asp-protocol</span></span>

<span data-ttu-id="50210-202">[ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) ÖZNITELIĞI, URL 'niz için bir protokol (gibi) belirtmek içindir `https` .</span><span class="sxs-lookup"><span data-stu-id="50210-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="50210-203">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="50210-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="50210-204">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="50210-205">Örnekteki ana bilgisayar adı localhost 'tur.</span><span class="sxs-lookup"><span data-stu-id="50210-205">The host name in the example is localhost.</span></span> <span data-ttu-id="50210-206">Tutturucu etiketi Yardımcısı, URL oluştururken Web sitesinin ortak etki alanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50210-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="50210-207">ASP-ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="50210-207">asp-host</span></span>

<span data-ttu-id="50210-208">[ASP-Host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) ÖZNITELIĞI, URL 'niz için bir ana bilgisayar adı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="50210-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="50210-209">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="50210-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="50210-210">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="50210-211">asp-sayfa</span><span class="sxs-lookup"><span data-stu-id="50210-211">asp-page</span></span>

<span data-ttu-id="50210-212">[ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) özniteliği Razor sayfalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50210-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="50210-213">Bir tutturucu etiketinin `href` öznitelik değerini belirli bir sayfaya ayarlamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50210-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="50210-214">Sayfa adının eğik çizgiyle ("/") önek olarak URL 'SI oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="50210-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="50210-215">Aşağıdaki örnek, katılımcı sayfasına işaret eder Razor :</span><span class="sxs-lookup"><span data-stu-id="50210-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="50210-216">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="50210-217">`asp-page`Özniteliği `asp-route` ,, ve öznitelikleriyle birbirini dışlıyor `asp-controller` `asp-action` .</span><span class="sxs-lookup"><span data-stu-id="50210-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="50210-218">Ancak, `asp-page` `asp-route-{value}` aşağıdaki işaretlemenin gösterdiği gibi yönlendirmeyi denetlemek için ile birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="50210-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="50210-219">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="50210-220">ASP-Page-Handler</span><span class="sxs-lookup"><span data-stu-id="50210-220">asp-page-handler</span></span>

<span data-ttu-id="50210-221">[ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) özniteliği Razor sayfalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50210-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="50210-222">Belirli sayfa işleyicileriyle bağlantı için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="50210-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="50210-223">Aşağıdaki sayfa işleyicisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="50210-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="50210-224">Sayfa modelinin ilişkili biçimlendirmesi `OnGetProfile` sayfa işleyicisine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="50210-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="50210-225">Göz önünde, `On<Verb>` sayfa işleyicisi yöntem adının ön eki öznitelik değerinde atlanacaktır `asp-page-handler` .</span><span class="sxs-lookup"><span data-stu-id="50210-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="50210-226">Yöntem zaman uyumsuz olduğunda, `Async` sonek de atlanır.</span><span class="sxs-lookup"><span data-stu-id="50210-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="50210-227">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="50210-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="50210-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50210-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
