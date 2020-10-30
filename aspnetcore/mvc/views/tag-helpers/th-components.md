---
title: ASP.NET Core içindeki etiket Yardımcısı bileşenleri
author: scottaddie
description: Etiket Yardımcısı bileşenlerinin ne olduğunu ve ASP.NET Core nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060566"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="1d06d-103">ASP.NET Core içindeki etiket Yardımcısı bileşenleri</span><span class="sxs-lookup"><span data-stu-id="1d06d-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="1d06d-104">[Scott Ade](https://twitter.com/Scott_Addie) ve [fiyaz bin hasan](https://github.com/fiyazbinhasan) tarafından</span><span class="sxs-lookup"><span data-stu-id="1d06d-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="1d06d-105">Etiket Yardımcısı bileşeni, sunucu tarafı kodundan HTML öğelerini koşullu olarak değiştirmenize veya eklemenize olanak sağlayan bir etiket yardımcıdır.</span><span class="sxs-lookup"><span data-stu-id="1d06d-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="1d06d-106">Bu özellik ASP.NET Core 2,0 veya üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="1d06d-107">ASP.NET Core iki yerleşik etiket Yardımcısı bileşeni içerir: `head` ve `body` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="1d06d-108">Bunlar <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers> ad alanında bulunur ve hem MVC hem de :::no-loc(Razor)::: sayfalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers> namespace and can be used in both MVC and :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="1d06d-109">Etiket Yardımcısı bileşenleri *_ViewImports. cshtml* 'de uygulamayla kayıt gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1d06d-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml* .</span></span>

<span data-ttu-id="1d06d-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d06d-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="1d06d-111">Uygulama alanları</span><span class="sxs-lookup"><span data-stu-id="1d06d-111">Use cases</span></span>

<span data-ttu-id="1d06d-112">Etiket Yardımcısı bileşenlerinin iki yaygın kullanım durumu şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1d06d-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="1d06d-113">İçine a ekleme `<link>` `<head>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="1d06d-114">İçine a ekleme `<script>` `<body>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="1d06d-115">Aşağıdaki bölümlerde bu kullanım durumları açıklanır.</span><span class="sxs-lookup"><span data-stu-id="1d06d-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="1d06d-116">HTML Head öğesine Ekle</span><span class="sxs-lookup"><span data-stu-id="1d06d-116">Inject into HTML head element</span></span>

<span data-ttu-id="1d06d-117">HTML öğesinin içinde `<head>` , CSS dosyaları genellıkle HTML öğesiyle içeri aktarılır `<link>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="1d06d-118">Aşağıdaki kod, `<link>` `<head>` etiket Yardımcısı bileşenini kullanarak öğesi öğesine bir öğesi çıkarır `head` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="1d06d-119">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="1d06d-119">In the preceding code:</span></span>

* <span data-ttu-id="1d06d-120">`AddressStyleTagHelperComponent` uygular <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent> .</span><span class="sxs-lookup"><span data-stu-id="1d06d-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="1d06d-121">Soyutlama:</span><span class="sxs-lookup"><span data-stu-id="1d06d-121">The abstraction:</span></span>
  * <span data-ttu-id="1d06d-122">İle sınıfının başlatılmasına izin verir <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext> .</span><span class="sxs-lookup"><span data-stu-id="1d06d-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="1d06d-123">HTML öğeleri eklemek veya değiştirmek için etiket Yardımcısı bileşenlerinin kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1d06d-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="1d06d-124"><xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.Order*>Özelliği, bileşenlerin işlendiği sırayı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1d06d-124">The <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="1d06d-125">`Order` bir uygulamada etiket Yardımcısı bileşenlerinin birden çok kullanımı olduğunda gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="1d06d-126"><xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.ProcessAsync*> yürütme bağlamının <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext.TagName*> özellik değerini olarak karşılaştırır `head` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-126"><xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="1d06d-127">Karşılaştırma true olarak değerlendirilirse, `_style` alanın IÇERIĞI HTML `<head>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="1d06d-128">HTML Body öğesine Ekle</span><span class="sxs-lookup"><span data-stu-id="1d06d-128">Inject into HTML body element</span></span>

<span data-ttu-id="1d06d-129">`body`Etiket Yardımcısı bileşeni, `<script>` öğesine bir öğesi ekleyebilir `<body>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="1d06d-130">Aşağıdaki kod bu tekniği göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="1d06d-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="1d06d-131">Öğesini depolamak için ayrı bir HTML dosyası kullanılır `<script>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="1d06d-132">HTML dosyası, kod temizleyici ve daha sürdürülebilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="1d06d-133">Önceki kod, *Taghelmakacıları/şablonları/AddressToolTipScript.html* içeriğini okur ve etiket Yardımcısı çıktısına ekler.</span><span class="sxs-lookup"><span data-stu-id="1d06d-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="1d06d-134">*AddressToolTipScript.html* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="1d06d-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="1d06d-135">Yukarıdaki kod, bir [önyükleme araç ipucu pencere öğesini](https://getbootstrap.com/docs/3.3/javascript/#tooltips) `<address>` bir özniteliği içeren herhangi bir öğeye bağlar `printable` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="1d06d-136">Bir fare işaretçisi öğenin üzerine geldiğinde efekt görünür.</span><span class="sxs-lookup"><span data-stu-id="1d06d-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="1d06d-137">Bir bileşeni kaydetme</span><span class="sxs-lookup"><span data-stu-id="1d06d-137">Register a Component</span></span>

<span data-ttu-id="1d06d-138">Uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna bir etiket Yardımcısı bileşeni eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="1d06d-139">Koleksiyona eklemenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="1d06d-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="1d06d-140">Hizmetler kapsayıcısı aracılığıyla kayıt</span><span class="sxs-lookup"><span data-stu-id="1d06d-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="1d06d-141">Dosya üzerinden kayıt :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="1d06d-141">Registration via :::no-loc(Razor)::: file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="1d06d-142">Sayfa modeli veya denetleyici aracılığıyla kaydolma</span><span class="sxs-lookup"><span data-stu-id="1d06d-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="1d06d-143">Hizmetler kapsayıcısı aracılığıyla kayıt</span><span class="sxs-lookup"><span data-stu-id="1d06d-143">Registration via services container</span></span>

<span data-ttu-id="1d06d-144">Etiket Yardımcısı bileşen sınıfı ile yönetilmemişse <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.ITagHelperComponentManager> , [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) sistemine kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="1d06d-145">Aşağıdaki `Startup.ConfigureServices` kod, `AddressStyleTagHelperComponent` ve `AddressScriptTagHelperComponent` sınıflarını [geçici bir yaşam süresine](xref:fundamentals/dependency-injection#lifetime-and-registration-options)kaydeder:</span><span class="sxs-lookup"><span data-stu-id="1d06d-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a><span data-ttu-id="1d06d-146">Dosya üzerinden kayıt :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="1d06d-146">Registration via :::no-loc(Razor)::: file</span></span>

<span data-ttu-id="1d06d-147">Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir :::no-loc(Razor)::: Sayfalar sayfasından veya BIR MVC görünümünden kayıt olabilir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-147">If the Tag Helper Component isn't registered with DI, it can be registered from a :::no-loc(Razor)::: Pages page or an MVC view.</span></span> <span data-ttu-id="1d06d-148">Bu teknik, eklenen işaretlemeyi ve bileşen yürütme sırasını bir dosyadan denetlemek için kullanılır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="1d06d-148">This technique is used for controlling the injected markup and the component execution order from a :::no-loc(Razor)::: file.</span></span>

<span data-ttu-id="1d06d-149">`ITagHelperComponentManager` Etiket Yardımcısı bileşenleri eklemek veya uygulamadan kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1d06d-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="1d06d-150">Aşağıdaki kod bu tekniği ile göstermektedir `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="1d06d-151">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="1d06d-151">In the preceding code:</span></span>

* <span data-ttu-id="1d06d-152">`@inject`Yönergesi bir örneği sağlar `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="1d06d-153">Örnek, `manager` dosyadaki aşağı akış erişimi için adlı bir değişkene atanır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="1d06d-153">The instance is assigned to a variable named `manager` for access downstream in the :::no-loc(Razor)::: file.</span></span>
* <span data-ttu-id="1d06d-154">Bir örneği, `AddressTagHelperComponent` uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="1d06d-155">`AddressTagHelperComponent` , ve parametrelerini kabul eden bir oluşturucuya uyacak şekilde `markup` değiştirilmiştir `order` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="1d06d-156">Belirtilen `markup` parametresi, aşağıdaki gibi ' de kullanılır `ProcessAsync` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="1d06d-157">Sayfa modeli veya denetleyici aracılığıyla kaydolma</span><span class="sxs-lookup"><span data-stu-id="1d06d-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="1d06d-158">Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir :::no-loc(Razor)::: sayfalar sayfa modelinden veya BIR MVC denetleyicisinden kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-158">If the Tag Helper Component isn't registered with DI, it can be registered from a :::no-loc(Razor)::: Pages page model or an MVC controller.</span></span> <span data-ttu-id="1d06d-159">Bu teknik, C# mantığını dosyalardan ayırmak için yararlıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="1d06d-159">This technique is useful for separating C# logic from :::no-loc(Razor)::: files.</span></span>

<span data-ttu-id="1d06d-160">Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="1d06d-161">Etiket Yardımcısı bileşeni, örneğin etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="1d06d-162">Aşağıdaki :::no-loc(Razor)::: sayfalar sayfa modelinde bu teknik gösterilmektedir `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-162">The following :::no-loc(Razor)::: Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="1d06d-163">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="1d06d-163">In the preceding code:</span></span>

* <span data-ttu-id="1d06d-164">Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="1d06d-165">Bir örneği, `AddressTagHelperComponent` uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="1d06d-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="1d06d-166">Bileşen oluşturma</span><span class="sxs-lookup"><span data-stu-id="1d06d-166">Create a Component</span></span>

<span data-ttu-id="1d06d-167">Özel bir etiket Yardımcısı bileşeni oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="1d06d-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="1d06d-168">Öğesinden türeten bir ortak sınıf oluşturun <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperComponentTagHelper> .</span><span class="sxs-lookup"><span data-stu-id="1d06d-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="1d06d-169">Sınıfına bir [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.HtmlTargetElementAttribute) öznitelik uygulayın.</span><span class="sxs-lookup"><span data-stu-id="1d06d-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="1d06d-170">Hedef HTML öğesinin adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="1d06d-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="1d06d-171">*Isteğe bağlı* : [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) türün IntelliSense 'de görüntülenmesini engellemek için sınıfa bir öznitelik uygulayın.</span><span class="sxs-lookup"><span data-stu-id="1d06d-171">*Optional* : Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="1d06d-172">Aşağıdaki kod, HTML öğesini hedefleyen özel bir etiket Yardımcısı bileşeni oluşturur `<address>` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="1d06d-173">`address`HTML işaretlemesini aşağıdaki gibi eklemek için özel etiket Yardımcısı bileşenini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1d06d-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="1d06d-174">Önceki `ProcessAsync` Yöntem, eşleşen öğeye için BELIRTILEN HTML 'yi çıkartır <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContent.SetHtmlContent*> `<address>` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="1d06d-175">Ekleme şu durumlarda oluşur:</span><span class="sxs-lookup"><span data-stu-id="1d06d-175">The injection occurs when:</span></span>

* <span data-ttu-id="1d06d-176">Yürütme bağlamının `TagName` özellik değeri eşittir `address` .</span><span class="sxs-lookup"><span data-stu-id="1d06d-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="1d06d-177">Karşılık gelen `<address>` öğenin bir `printable` özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="1d06d-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="1d06d-178">Örneğin, `if` aşağıdaki öğeyi işlerken ifade true olarak değerlendirilir `<address>` :</span><span class="sxs-lookup"><span data-stu-id="1d06d-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="1d06d-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1d06d-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
