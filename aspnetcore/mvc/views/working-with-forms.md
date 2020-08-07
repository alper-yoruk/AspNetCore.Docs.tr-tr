---
title: ASP.NET Core formlardaki etiket yardımcıları
author: rick-anderson
description: Formlarla kullanılan yerleşik etiket yardımcılarını açıklar.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/working-with-forms
ms.openlocfilehash: 833939417c4973c61eca4d41aaeabe7856680190
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913860"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="b0f8a-103">ASP.NET Core formlardaki etiket yardımcıları</span><span class="sxs-lookup"><span data-stu-id="b0f8a-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="b0f8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Davve Patıı](https://twitter.com/Dave_Paquette)ve [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="b0f8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="b0f8a-105">Bu belge, formlarda ve genellikle form üzerinde kullanılan HTML öğeleriyle çalışmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="b0f8a-106">HTML [form](https://www.w3.org/TR/html401/interact/forms.html) öğesi, Web uygulamalarının sunucuya veri geri göndermek için kullanacağı birincil mekanizmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="b0f8a-107">Bu belgenin çoğunda [Etiket Yardımcıları](tag-helpers/intro.md) ve BUNLARıN güçlü HTML formları oluşturma konusunda nasıl yardımcı olabilecekleri açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="b0f8a-108">Bu belgeyi okuyabilmeniz [Için yardımcıları etiketleyerek](tag-helpers/intro.md) okumanız önerilir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="b0f8a-109">Birçok durumda, HTML Yardımcıları belirli bir etiket Yardımcısı için alternatif bir yaklaşım sağlar, ancak bu etiket yardımcıların HTML yardımcılarını değiştirmez ve her HTML Yardımcısı için bir etiket Yardımcısı olmadığını bilmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="b0f8a-110">Bir HTML Yardımcısı alternatifi varsa, bu, bahsedilir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="b0f8a-111">Form etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-111">The Form Tag Helper</span></span>

<span data-ttu-id="b0f8a-112">[Form](https://www.w3.org/TR/html401/interact/forms.html) etiketi Yardımcısı:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="b0f8a-113">[\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` MVC denetleyicisi eylemi veya adlandırılmış yol için html öznitelik değeri oluşturur</span><span class="sxs-lookup"><span data-stu-id="b0f8a-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="b0f8a-114">Siteler arası istek yasaklamasını engellemek için gizli bir [Istek doğrulama belirteci](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) ÜRETIR ( `[ValidateAntiForgeryToken]` http post eylem yönteminde özniteliğiyle birlikte kullanıldığında)</span><span class="sxs-lookup"><span data-stu-id="b0f8a-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="b0f8a-115">`asp-route-<Parameter Name>`, `<Parameter Name>` Yol değerlerine eklendiği özniteliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="b0f8a-116">`routeValues`Ve için parametreler `Html.BeginForm` , `Html.BeginRouteForm` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="b0f8a-117">Bir HTML Yardımcısı alternatifi `Html.BeginForm` ve`Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="b0f8a-118">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-118">Sample:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="b0f8a-119">Yukarıdaki form etiketi Yardımcısı aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="b0f8a-120">MVC çalışma zamanı, `action` form etiketi yardımcı öznitelikleri ve ' den öznitelik değeri `asp-controller` oluşturur `asp-action` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="b0f8a-121">Form etiketi Yardımcısı ayrıca siteler arası istek sahteciliği (HTTP POST eylem yönteminde özniteliğiyle kullanıldığında) engellemek için gizli bir [Istek doğrulama belirteci](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oluşturur `[ValidateAntiForgeryToken]` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="b0f8a-122">Bir saf HTML formunun siteler arası istek sahteciliğini önleme 'den korunması zordur, form etiketi Yardımcısı bu hizmeti sizin için sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="b0f8a-123">Adlandırılmış yol kullanma</span><span class="sxs-lookup"><span data-stu-id="b0f8a-123">Using a named route</span></span>

<span data-ttu-id="b0f8a-124">`asp-route`Etiket Yardımcısı özniteliği, HTML özniteliği için de biçimlendirme oluşturabilir `action` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="b0f8a-125">Adlı [yolu](../../fundamentals/routing.md) içeren bir uygulama `register` , kayıt sayfası için aşağıdaki biçimlendirmeyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="b0f8a-126">*Görünümler/hesap* klasöründeki görünümlerin birçoğu ( *bireysel kullanıcı hesaplarıyla*yeni bir Web uygulaması oluşturduğunuzda oluşturulur), [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="b0f8a-127">Yerleşik şablonlarla, `returnUrl` yetkili bir kaynağa erişmeye çalıştığınızda ancak kimliği doğrulanmamış veya yetkilendirilmeyen otomatik olarak doldurulur.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="b0f8a-128">Yetkisiz erişim yapmaya çalıştığınızda güvenlik ara yazılımı sizi, küme ile oturum açma sayfasına yönlendirir `returnUrl` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="b0f8a-129">Form eylemi etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="b0f8a-130">Form eylemi etiketi Yardımcısı, `formaction` oluşturulan `<button ...>` veya etiketteki özniteliği oluşturur `<input type="image" ...>` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="b0f8a-131">`formaction`Özniteliği bir formun verilerini nereden gönderdiğini denetler.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="b0f8a-132">[\<input>](https://www.w3.org/wiki/HTML/Elements/input)Türü `image` ve öğeleri öğelerine bağlanır [\<button>](https://www.w3.org/wiki/HTML/Elements/button) .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="b0f8a-133">Form eylemi etiketi Yardımcısı, [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` `formaction` ilgili öğe için hangi bağlantının oluşturulduğunu denetlemek için çeşitli AnchorTagHelper özniteliklerinin kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="b0f8a-134">Değerini denetlemek için desteklenen [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) öznitelikleri `formaction` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="b0f8a-135">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="b0f8a-135">Attribute</span></span>|<span data-ttu-id="b0f8a-136">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b0f8a-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="b0f8a-137">ASP-Controller</span><span class="sxs-lookup"><span data-stu-id="b0f8a-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="b0f8a-138">Denetleyicinin adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-138">The name of the controller.</span></span>|
|[<span data-ttu-id="b0f8a-139">ASP-eylem</span><span class="sxs-lookup"><span data-stu-id="b0f8a-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="b0f8a-140">Eylem yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-140">The name of the action method.</span></span>|
|[<span data-ttu-id="b0f8a-141">ASP-alanı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="b0f8a-142">Alanın adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-142">The name of the area.</span></span>|
|[<span data-ttu-id="b0f8a-143">asp-sayfa</span><span class="sxs-lookup"><span data-stu-id="b0f8a-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="b0f8a-144">RazorSayfanın adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="b0f8a-145">ASP-Page-Handler</span><span class="sxs-lookup"><span data-stu-id="b0f8a-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="b0f8a-146">RazorSayfa işleyicisinin adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="b0f8a-147">ASP-Route</span><span class="sxs-lookup"><span data-stu-id="b0f8a-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="b0f8a-148">Yolun adı.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-148">The name of the route.</span></span>|
|[<span data-ttu-id="b0f8a-149">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="b0f8a-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="b0f8a-150">Tek bir URL yol değeri.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-150">A single URL route value.</span></span> <span data-ttu-id="b0f8a-151">Örneğin, `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="b0f8a-152">ASP-All-Route-Data</span><span class="sxs-lookup"><span data-stu-id="b0f8a-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="b0f8a-153">Tüm rota değerleri.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-153">All route values.</span></span>|
|[<span data-ttu-id="b0f8a-154">ASP-Fragment</span><span class="sxs-lookup"><span data-stu-id="b0f8a-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="b0f8a-155">URL parçası.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="b0f8a-156">Denetleyiciye gönder örneği</span><span class="sxs-lookup"><span data-stu-id="b0f8a-156">Submit to controller example</span></span>

<span data-ttu-id="b0f8a-157">Aşağıdaki biçimlendirme, formu `Index` `HomeController` giriş veya düğme seçildiğinde eyleme gönderir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="b0f8a-158">Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="b0f8a-159">Sayfa örneğine gönder</span><span class="sxs-lookup"><span data-stu-id="b0f8a-159">Submit to page example</span></span>

<span data-ttu-id="b0f8a-160">Aşağıdaki biçimlendirme formu `About` Razor sayfasına gönderir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="b0f8a-161">Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="b0f8a-162">Yönlendirme örneğine gönder</span><span class="sxs-lookup"><span data-stu-id="b0f8a-162">Submit to route example</span></span>

<span data-ttu-id="b0f8a-163">Uç noktayı göz önünde bulundurun `/Home/Test` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="b0f8a-164">Aşağıdaki biçimlendirme formu `/Home/Test` uç noktaya gönderir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="b0f8a-165">Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="b0f8a-166">Giriş etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-166">The Input Tag Helper</span></span>

<span data-ttu-id="b0f8a-167">Giriş etiketi Yardımcısı, bir HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) öğesini Razor görünüminizdeki bir model ifadesine bağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="b0f8a-168">Söz dizimi:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="b0f8a-169">Giriş etiketi Yardımcısı:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="b0f8a-170">`id` `name` Özniteliğinde belirtilen ifade adı için ve HTML özniteliklerini üretir `asp-for` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="b0f8a-171">`asp-for="Property1.Property2"`değerine eşdeğerdir `m => m.Property1.Property2` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="b0f8a-172">İfadenin adı, öznitelik değeri için kullanılan şeydir `asp-for` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="b0f8a-173">Ek bilgi için [ifade adları](#expression-names) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="b0f8a-174">Model `type` özelliğine uygulanan model türüne ve [veri ek açıklaması](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) özniteliklerine göre html öznitelik değerini ayarlar</span><span class="sxs-lookup"><span data-stu-id="b0f8a-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="b0f8a-175">`type`BELIRTILDIĞINDE html öznitelik değerinin üzerine yazılmaz</span><span class="sxs-lookup"><span data-stu-id="b0f8a-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="b0f8a-176">Model özelliklerine uygulanan [veri ek açıklama](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) özniteliklerinden [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) doğrulama öznitelikleri oluşturur</span><span class="sxs-lookup"><span data-stu-id="b0f8a-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="b0f8a-177">, Ve ile çakışan bir HTML yardımcı özelliğine sahiptir `Html.TextBoxFor` `Html.EditorFor` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="b0f8a-178">Ayrıntılar için bkz. **giriş etiketi Yardımcısı Için HTML Yardımcısı alternatifleri** .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="b0f8a-179">Güçlü yazma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-179">Provides strong typing.</span></span> <span data-ttu-id="b0f8a-180">Özelliğin adı değişirse ve etiket yardımcısını güncelleştirmezseniz aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

<span data-ttu-id="b0f8a-181">`Input`Etiket Yardımcısı, HTML `type` özniteliğini .net türüne göre ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="b0f8a-182">Aşağıdaki tabloda bazı ortak .NET türleri ve oluşturulan HTML türü listelenmekte (her .NET türü listelenmemiştir).</span><span class="sxs-lookup"><span data-stu-id="b0f8a-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="b0f8a-183">.NET türü</span><span class="sxs-lookup"><span data-stu-id="b0f8a-183">.NET type</span></span>|<span data-ttu-id="b0f8a-184">Giriş Türü</span><span class="sxs-lookup"><span data-stu-id="b0f8a-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="b0f8a-185">Bool</span><span class="sxs-lookup"><span data-stu-id="b0f8a-185">Bool</span></span>|<span data-ttu-id="b0f8a-186">Type = "onay kutusu"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-186">type="checkbox"</span></span>|
|<span data-ttu-id="b0f8a-187">Dize</span><span class="sxs-lookup"><span data-stu-id="b0f8a-187">String</span></span>|<span data-ttu-id="b0f8a-188">Type = "metin"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-188">type="text"</span></span>|
|<span data-ttu-id="b0f8a-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="b0f8a-189">DateTime</span></span>|<span data-ttu-id="b0f8a-190">Type =["TarihSaat-yerel"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="b0f8a-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="b0f8a-191">Bayt</span><span class="sxs-lookup"><span data-stu-id="b0f8a-191">Byte</span></span>|<span data-ttu-id="b0f8a-192">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-192">type="number"</span></span>|
|<span data-ttu-id="b0f8a-193">int</span><span class="sxs-lookup"><span data-stu-id="b0f8a-193">Int</span></span>|<span data-ttu-id="b0f8a-194">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-194">type="number"</span></span>|
|<span data-ttu-id="b0f8a-195">Tek, Çift</span><span class="sxs-lookup"><span data-stu-id="b0f8a-195">Single, Double</span></span>|<span data-ttu-id="b0f8a-196">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-196">type="number"</span></span>|

<span data-ttu-id="b0f8a-197">Aşağıdaki tabloda, giriş etiketi Yardımcısı 'nın belirli giriş türleriyle eşleşecağı bazı ortak [veri ek açıklamaları](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) (her doğrulama özniteliği listelenmez) gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="b0f8a-198">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="b0f8a-198">Attribute</span></span>|<span data-ttu-id="b0f8a-199">Giriş Türü</span><span class="sxs-lookup"><span data-stu-id="b0f8a-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="b0f8a-200">EmailAddress</span><span class="sxs-lookup"><span data-stu-id="b0f8a-200">[EmailAddress]</span></span>|<span data-ttu-id="b0f8a-201">Type = "e-posta"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-201">type="email"</span></span>|
|<span data-ttu-id="b0f8a-202">'Deki</span><span class="sxs-lookup"><span data-stu-id="b0f8a-202">[Url]</span></span>|<span data-ttu-id="b0f8a-203">Type = "URL"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-203">type="url"</span></span>|
|<span data-ttu-id="b0f8a-204">[Hiddenınput]</span><span class="sxs-lookup"><span data-stu-id="b0f8a-204">[HiddenInput]</span></span>|<span data-ttu-id="b0f8a-205">Type = "Hidden"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-205">type="hidden"</span></span>|
|<span data-ttu-id="b0f8a-206">Numarası</span><span class="sxs-lookup"><span data-stu-id="b0f8a-206">[Phone]</span></span>|<span data-ttu-id="b0f8a-207">Type = "tel"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-207">type="tel"</span></span>|
|<span data-ttu-id="b0f8a-208">[DataType (DataType. Password)]</span><span class="sxs-lookup"><span data-stu-id="b0f8a-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="b0f8a-209">Type = "Password"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-209">type="password"</span></span>|
|<span data-ttu-id="b0f8a-210">[DataType (DataType. Date)]</span><span class="sxs-lookup"><span data-stu-id="b0f8a-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="b0f8a-211">Type = "Date"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-211">type="date"</span></span>|
|<span data-ttu-id="b0f8a-212">[DataType (DataType. Time)]</span><span class="sxs-lookup"><span data-stu-id="b0f8a-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="b0f8a-213">yazın = "Time"</span><span class="sxs-lookup"><span data-stu-id="b0f8a-213">type="time"</span></span>|

<span data-ttu-id="b0f8a-214">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="b0f8a-215">Yukarıdaki kod, aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="b0f8a-216">Ve özelliklerine uygulanan veri ek açıklamaları `Email` `Password` modelde meta veriler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="b0f8a-217">Giriş etiketi Yardımcısı, model meta verilerini kullanır ve [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` öznitelikleri üretir (bkz. [model doğrulama](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="b0f8a-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="b0f8a-218">Bu öznitelikler, giriş alanlarına iliştirilecek Doğrulayıcıları anlatmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="b0f8a-219">Bu unobtrusive HTML5 ve [jQuery](https://jquery.com/) doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="b0f8a-220">Unobtrusive öznitelikleri biçimindedir `data-val-rule="Error Message"` , burada kural doğrulama kuralının adıdır (örneğin,, `data-val-required` `data-val-email` `data-val-maxlength` vb.) Öznitelikte bir hata iletisi sağlanırsa, özniteliği için değer olarak görüntülenir `data-val-rule` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="b0f8a-221">Ayrıca, `data-val-ruleName-argumentName="argumentValue"` kural hakkında ek ayrıntılar sağlayan formun öznitelikleri de vardır, örneğin `data-val-maxlength-max="1024"` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="b0f8a-222">Giriş etiketi Yardımcısı için HTML Yardımcısı alternatifleri</span><span class="sxs-lookup"><span data-stu-id="b0f8a-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="b0f8a-223">`Html.TextBox`, `Html.TextBoxFor` `Html.Editor` ve, `Html.EditorFor` giriş etiketi Yardımcısı ile çakışan özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="b0f8a-224">Giriş etiketi Yardımcısı otomatik olarak `type` özniteliği ayarlar ve bu şekilde `Html.TextBox` `Html.TextBoxFor` çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="b0f8a-225">`Html.Editor`ve `Html.EditorFor` koleksiyonlar, karmaşık nesneler ve şablonlar; giriş etiketi Yardımcısı değildir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="b0f8a-226">Giriş etiketi Yardımcısı `Html.EditorFor` ve kesin olarak `Html.TextBoxFor` yazılmış (lambda ifadeleri kullanır) `Html.TextBox` ve `Html.Editor` değildir (ifade adları kullanır).</span><span class="sxs-lookup"><span data-stu-id="b0f8a-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="b0f8a-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="b0f8a-227">HtmlAttributes</span></span>

<span data-ttu-id="b0f8a-228">`@Html.Editor()`ve `@Html.EditorFor()` `ViewDataDictionary` varsayılan şablonlarını yürütürken adlı özel bir giriş kullanın `htmlAttributes` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="b0f8a-229">Bu davranış, isteğe bağlı olarak parametreler kullanılarak genişletilmiş şekilde belirlenir `additionalViewData` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="b0f8a-230">"HtmlAttributes" anahtarı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="b0f8a-231">"HtmlAttributes" anahtarı, `htmlAttributes` gibi giriş yardımcılarını geçirilmiş nesneye benzer şekilde işlenir `@Html.TextBox()` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="b0f8a-232">İfade adları</span><span class="sxs-lookup"><span data-stu-id="b0f8a-232">Expression names</span></span>

<span data-ttu-id="b0f8a-233">`asp-for`Öznitelik değeri bir `ModelExpression` lambda ifadesinin bir ve sağ tarafıdır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="b0f8a-234">Bu nedenle `asp-for="Property1"` , `m => m.Property1` ile öneki gerekmez, oluşturulan kodda olur `Model` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="b0f8a-235">" \@ " Karakterini kullanarak bir satır içi ifade başlatabilir ve öğesinden önce taşıyabilirsiniz `m.` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="b0f8a-236">Şunları üretir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="b0f8a-237">Koleksiyon özellikleriyle, `asp-for="CollectionProperty[23].Member"` değeri olduğu gibi aynı adı oluşturur `asp-for="CollectionProperty[i].Member"` `i` `23` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="b0f8a-238">ASP.NET Core MVC değeri hesapladığında, `ModelExpression` dahil olmak üzere çeşitli kaynakları inceler `ModelState` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="b0f8a-239">Göz önünde bulundurun `<input type="text" asp-for="@Name">` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="b0f8a-240">Hesaplanan `value` öznitelik, öğesinden gelen ilk null olmayan değerdir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="b0f8a-241">`ModelState`"Name" anahtarına sahip giriş.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="b0f8a-242">İfadenin sonucu `Model.Name` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="b0f8a-243">Alt özelliklerde gezinme</span><span class="sxs-lookup"><span data-stu-id="b0f8a-243">Navigating child properties</span></span>

<span data-ttu-id="b0f8a-244">Ayrıca, görünüm modelinin özellik yolunu kullanarak alt Özellikler ' e gidebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="b0f8a-245">Alt özellik içeren daha karmaşık bir model sınıfı düşünün `Address` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="b0f8a-246">Görünümünde, şu şekilde bağlandık `Address.AddressLine1` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="b0f8a-247">İçin aşağıdaki HTML oluşturulur `Address.AddressLine1` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="b0f8a-248">İfade adları ve koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="b0f8a-248">Expression names and Collections</span></span>

<span data-ttu-id="b0f8a-249">Örnek, bir dizisi içeren bir model `Colors` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="b0f8a-250">Eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="b0f8a-251">Aşağıda Razor belirli bir öğeye nasıl erişebileceğiniz gösterilmektedir `Color` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="b0f8a-252">*Views/Shared/EditorTemplates/String. cshtml* şablonu:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="b0f8a-253">Örnek kullanarak `List<T>` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="b0f8a-254">Aşağıda Razor bir koleksiyonun üzerinde nasıl yinelemi yapılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="b0f8a-255">*Views/Shared/EditorTemplates/TodoItem. cshtml* şablonu:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="b0f8a-256">`foreach`değer bir `asp-for` veya eşdeğer bağlamda kullanılacaksa, mümkünse kullanılması gerekir `Html.DisplayFor` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="b0f8a-257">Genel olarak, `for` `foreach` bir Numaralandırıcı ayırması gerekmiyorsa (senaryo buna izin veriyorsa) daha iyidir; ancak, bir LINQ ifadesinde bir dizin oluşturucunun değerlendirilmesi pahalı olabilir ve simge durumuna küçültülmüş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="b0f8a-258">Yukarıdaki açıklamalı örnek kod, `@` listedeki her birine erişmek için lambda ifadesinin işleçle nasıl değiştirileceğini gösterir `ToDoItem` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="b0f8a-259">TextArea etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="b0f8a-260">`Textarea Tag Helper`Etiket Yardımcısı giriş etiketi Yardımcısı ile benzerdir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="b0f8a-261">, `id` Ve `name` özniteliklerini ve bir öğe için modelden veri doğrulama özniteliklerini üretir [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="b0f8a-262">Güçlü yazma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-262">Provides strong typing.</span></span>

* <span data-ttu-id="b0f8a-263">HTML Yardımcısı alternatifi:`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="b0f8a-264">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="b0f8a-265">Aşağıdaki HTML oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="b0f8a-266">Etiket etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-266">The Label Tag Helper</span></span>

* <span data-ttu-id="b0f8a-267">Bir `for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) ifade adı için bir öğe üzerinde etiket başlığı ve özniteliği oluşturur</span><span class="sxs-lookup"><span data-stu-id="b0f8a-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="b0f8a-268">HTML Yardımcısı alternatifi: `Html.LabelFor` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="b0f8a-269">, `Label Tag Helper` Saf HTML etiket öğesi üzerinde aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="b0f8a-270">Öznitelikten açıklayıcı etiket değerini otomatik olarak alırsınız `Display` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="b0f8a-271">İstenen görünen ad zaman içinde değişebilir ve `Display` öznitelik ve etiket etiketi Yardımcısı 'nın birleşimi, `Display` kullanıldığı her yere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="b0f8a-272">Kaynak kodunda daha az biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="b0f8a-272">Less markup in source code</span></span>

* <span data-ttu-id="b0f8a-273">Model özelliğiyle güçlü yazma.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-273">Strong typing with the model property.</span></span>

<span data-ttu-id="b0f8a-274">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="b0f8a-275">Öğesi için aşağıdaki HTML oluşturulur `<label>` :</span><span class="sxs-lookup"><span data-stu-id="b0f8a-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="b0f8a-276">Etiket etiketi Yardımcısı, `for` öğesiyle ILIŞKILI kimlik olan "e-posta" öznitelik değerini oluşturdu `<input>` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="b0f8a-277">Etiket Yardımcıları, `id` `for` doğru şekilde ilişkilendirilebilen tutarlı ve öğeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="b0f8a-278">Bu örnekteki başlık, `Display` özniteliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="b0f8a-279">Modelde bir `Display` öznitelik yoksa, başlık ifadenin Özellik adı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="b0f8a-280">Doğrulama etiketi yardımcıları</span><span class="sxs-lookup"><span data-stu-id="b0f8a-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="b0f8a-281">İki doğrulama etiketi yardımcıları vardır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="b0f8a-282">`Validation Message Tag Helper`(Bu, modelinizde tek bir özellik için bir doğrulama iletisi gösterir) ve `Validation Summary Tag Helper` (doğrulama hatalarının özetini görüntüler).</span><span class="sxs-lookup"><span data-stu-id="b0f8a-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="b0f8a-283">, `Input Tag Helper` Model sınıflarınızda bulunan veri ek açıklaması özniteliklerini temel alan giriş ÖĞELERINE HTML5 istemci tarafı doğrulama öznitelikleri ekler.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="b0f8a-284">Doğrulama de sunucuda gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-284">Validation is also performed on the server.</span></span> <span data-ttu-id="b0f8a-285">Doğrulama etiketi Yardımcısı, bir doğrulama hatası oluştuğunda bu hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="b0f8a-286">Doğrulama Iletisi etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="b0f8a-287">[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` Belirtilen model özelliğinin giriş alanındaki doğrulama hatası mesajlarını bağlayan [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) öğesine HTML5 özniteliğini ekler.  </span><span class="sxs-lookup"><span data-stu-id="b0f8a-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="b0f8a-288">İstemci tarafı doğrulama hatası oluştuğunda [jQuery](https://jquery.com/) , öğesinde hata iletisini görüntüler `<span>` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="b0f8a-289">Doğrulama de sunucuda gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-289">Validation also takes place on the server.</span></span> <span data-ttu-id="b0f8a-290">İstemciler JavaScript devre dışı bırakılmış olabilir ve bazı doğrulamalar yalnızca sunucu tarafında yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="b0f8a-291">HTML Yardımcısı alternatifi:`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="b0f8a-292">, `Validation Message Tag Helper` `asp-validation-for` Bir HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) öğesinde özniteliğiyle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="b0f8a-293">Doğrulama Iletisi etiketi Yardımcısı aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="b0f8a-294">Genellikle `Validation Message Tag Helper` `Input` aynı özellik için bir etiket Yardımcısı ' nı kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="b0f8a-295">Bunun yapılması, hataya neden olan girişin yakınında herhangi bir doğrulama hata iletisi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="b0f8a-296">İstemci tarafı doğrulaması için doğru JavaScript ve [jQuery](https://jquery.com/) betik başvurularını içeren bir görünümsiniz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="b0f8a-297">Daha fazla bilgi için bkz. [model doğrulaması](../models/validation.md) .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="b0f8a-298">Sunucu tarafı doğrulama hatası oluştuğunda (örneğin, özel sunucu tarafı doğrulama veya istemci tarafı doğrulaması devre dışı bırakılmışsa), MVC bu hata iletisini öğenin gövdesi olarak koyar `<span>` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="b0f8a-299">Doğrulama Özeti etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="b0f8a-300">`<div>`Özniteliği olan öğeleri hedefler `asp-validation-summary`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="b0f8a-301">HTML Yardımcısı alternatifi:`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="b0f8a-302">, `Validation Summary Tag Helper` Doğrulama iletilerinin özetini göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="b0f8a-303">`asp-validation-summary`Öznitelik değeri, aşağıdakilerden herhangi biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="b0f8a-304">ASP-doğrulama-Özet</span><span class="sxs-lookup"><span data-stu-id="b0f8a-304">asp-validation-summary</span></span>|<span data-ttu-id="b0f8a-305">Görünen doğrulama iletileri</span><span class="sxs-lookup"><span data-stu-id="b0f8a-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="b0f8a-306">ValidationSummary. All</span><span class="sxs-lookup"><span data-stu-id="b0f8a-306">ValidationSummary.All</span></span>|<span data-ttu-id="b0f8a-307">Özellik ve model düzeyi</span><span class="sxs-lookup"><span data-stu-id="b0f8a-307">Property and model level</span></span>|
|<span data-ttu-id="b0f8a-308">Yalnızca ValidationSummary. model</span><span class="sxs-lookup"><span data-stu-id="b0f8a-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="b0f8a-309">Model</span><span class="sxs-lookup"><span data-stu-id="b0f8a-309">Model</span></span>|
|<span data-ttu-id="b0f8a-310">ValidationSummary. None</span><span class="sxs-lookup"><span data-stu-id="b0f8a-310">ValidationSummary.None</span></span>|<span data-ttu-id="b0f8a-311">Yok</span><span class="sxs-lookup"><span data-stu-id="b0f8a-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="b0f8a-312">Örnek</span><span class="sxs-lookup"><span data-stu-id="b0f8a-312">Sample</span></span>

<span data-ttu-id="b0f8a-313">Aşağıdaki örnekte, veri modelinde, `DataAnnotation` öğesinde doğrulama hatası iletileri üreten öznitelikler vardır `<input>` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="b0f8a-314">Doğrulama hatası oluştuğunda, doğrulama etiketi Yardımcısı şu hata iletisini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="b0f8a-315">Oluşturulan HTML (model geçerli olduğunda):</span><span class="sxs-lookup"><span data-stu-id="b0f8a-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="b0f8a-316">Etiket Seç Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b0f8a-316">The Select Tag Helper</span></span>

* <span data-ttu-id="b0f8a-317">Modelinizin özellikleri için [Select](https://www.w3.org/wiki/HTML/Elements/select) ve ilişkili [seçenek](https://www.w3.org/wiki/HTML/Elements/option) öğeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="b0f8a-318">Bir HTML Yardımcısı alternatifi `Html.DropDownListFor` ve`Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="b0f8a-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="b0f8a-319">, `Select Tag Helper` `asp-for` [Select](https://www.w3.org/wiki/HTML/Elements/select) öğesi için model özelliği adını belirtir ve `asp-items` [seçenek](https://www.w3.org/wiki/HTML/Elements/option) öğelerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="b0f8a-320">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-320">For example:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="b0f8a-321">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="b0f8a-322">`Index`Yöntemi `CountryViewModel` öğesini başlatır, seçilen ülkeyi ayarlar ve görünüme geçirir `Index` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="b0f8a-323">HTTP POST `Index` yöntemi seçimi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="b0f8a-324">`Index`Görünüm:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="b0f8a-325">Aşağıdaki HTML 'yi üreten ("CA" seçiliyken):</span><span class="sxs-lookup"><span data-stu-id="b0f8a-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="b0f8a-326">`ViewBag` `ViewData` Etiket Seç Yardımcısı ile veya kullanmayı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="b0f8a-327">Bir görünüm modeli, MVC meta verileri sağlamaya ve genellikle daha az soruna neden olacak daha sağlamdır.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="b0f8a-328">`asp-for`Öznitelik değeri özel bir durumdur ve bir `Model` ön ek gerektirmez, diğer etiket Yardımcısı öznitelikleri olur (gibi `asp-items` )</span><span class="sxs-lookup"><span data-stu-id="b0f8a-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="b0f8a-329">Sabit Listesi bağlama</span><span class="sxs-lookup"><span data-stu-id="b0f8a-329">Enum binding</span></span>

<span data-ttu-id="b0f8a-330">Genellikle `<select>` bir `enum` özellikle kullanılması ve `SelectListItem` değerlerden öğeleri oluşturmak kullanışlıdır `enum` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="b0f8a-331">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="b0f8a-332">Yöntemi, bir `GetEnumSelectList` `SelectList` numaralandırma için bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="b0f8a-333">`Display`Daha zengin bir kullanıcı arabirimi almak için, Numaralandırıcı listenizi özniteliğiyle işaretleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="b0f8a-334">Aşağıdaki HTML oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="b0f8a-335">Seçenek grubu</span><span class="sxs-lookup"><span data-stu-id="b0f8a-335">Option Group</span></span>

<span data-ttu-id="b0f8a-336">HTML [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) öğesi, görünüm modeli bir veya daha fazla nesne içerdiğinde oluşturulur `SelectListGroup` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="b0f8a-337">`CountryViewModelGroup` `SelectListItem` Öğeleri "Kuzey Amerika" ve "Avrupa" gruplarında gruplandırır:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="b0f8a-338">İki grup aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-338">The two groups are shown below:</span></span>

![seçenek grubu örneği](working-with-forms/_static/grp.png)

<span data-ttu-id="b0f8a-340">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="b0f8a-341">Çoklu seçim</span><span class="sxs-lookup"><span data-stu-id="b0f8a-341">Multiple select</span></span>

<span data-ttu-id="b0f8a-342">Öznitelikte belirtilen özellik bir ise, select etiketi Yardımcısı otomatik olarak [birden çok = "çoklu"](https://w3c.github.io/html-reference/select.html) özniteliği oluşturur `asp-for` `IEnumerable` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="b0f8a-343">Örneğin, aşağıdaki model verildiğinde:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="b0f8a-344">Aşağıdaki görünümle:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-344">With the following view:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="b0f8a-345">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="b0f8a-346">Seçim yok</span><span class="sxs-lookup"><span data-stu-id="b0f8a-346">No selection</span></span>

<span data-ttu-id="b0f8a-347">Birden çok sayfada "belirtilmemiş" seçeneğini kullanarak kendinizi bulursanız, HTML 'yi yinelemeyi ortadan kaldırmak için bir şablon oluşturabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="b0f8a-348">*Views/Shared/EditorTemplates/CountryViewModel. cshtml* şablonu:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="b0f8a-349">HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) öğelerinin eklenmesi *hiçbir seçim* durumuyla sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="b0f8a-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="b0f8a-350">Örneğin, aşağıdaki görünüm ve eylem yöntemi yukarıdaki koda benzer HTML oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b0f8a-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="b0f8a-351">`<option>` `selected="selected"` Geçerli değere bağlı olarak doğru öğe seçilir (özniteliği içerir) `Country` .</span><span class="sxs-lookup"><span data-stu-id="b0f8a-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="b0f8a-352">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b0f8a-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="b0f8a-353">HTML form öğesi</span><span class="sxs-lookup"><span data-stu-id="b0f8a-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="b0f8a-354">İstek doğrulama belirteci</span><span class="sxs-lookup"><span data-stu-id="b0f8a-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="b0f8a-355">Iattributeadapter arabirimi</span><span class="sxs-lookup"><span data-stu-id="b0f8a-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="b0f8a-356">Bu belge için kod parçacıkları</span><span class="sxs-lookup"><span data-stu-id="b0f8a-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
