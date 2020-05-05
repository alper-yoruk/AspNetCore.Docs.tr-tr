---
title: ASP.NET Core içindeki etiket Yardımcısı bileşenleri
author: scottaddie
description: Etiket Yardımcısı bileşenlerinin ne olduğunu ve ASP.NET Core nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: df118cdc8346b99e4e5c60c9f0441c963543f4b4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767518"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="c19ba-103">ASP.NET Core içindeki etiket Yardımcısı bileşenleri</span><span class="sxs-lookup"><span data-stu-id="c19ba-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="c19ba-104">[Scott Ade](https://twitter.com/Scott_Addie) ve [fiyaz bin hasan](https://github.com/fiyazbinhasan) tarafından</span><span class="sxs-lookup"><span data-stu-id="c19ba-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="c19ba-105">Etiket Yardımcısı bileşeni, sunucu tarafı kodundan HTML öğelerini koşullu olarak değiştirmenize veya eklemenize olanak sağlayan bir etiket yardımcıdır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="c19ba-106">Bu özellik ASP.NET Core 2,0 veya üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="c19ba-107">ASP.NET Core iki yerleşik etiket Yardımcısı bileşeni içerir: `head` ve. `body`</span><span class="sxs-lookup"><span data-stu-id="c19ba-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="c19ba-108">Bunlar <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> ad alanında bulunur ve hem MVC hem Razor de sayfalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="c19ba-109">Etiket Yardımcısı bileşenleri *_ViewImports. cshtml*'de uygulamayla kayıt gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="c19ba-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="c19ba-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c19ba-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="c19ba-111">Uygulama alanları</span><span class="sxs-lookup"><span data-stu-id="c19ba-111">Use cases</span></span>

<span data-ttu-id="c19ba-112">Etiket Yardımcısı bileşenlerinin iki yaygın kullanım durumu şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c19ba-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="c19ba-113">İçine a `<link>` ekleme `<head>`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="c19ba-114">İçine a `<script>` ekleme `<body>`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="c19ba-115">Aşağıdaki bölümlerde bu kullanım durumları açıklanır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="c19ba-116">HTML Head öğesine Ekle</span><span class="sxs-lookup"><span data-stu-id="c19ba-116">Inject into HTML head element</span></span>

<span data-ttu-id="c19ba-117">HTML `<head>` öğesinin IÇINDE, CSS dosyaları genellikle HTML `<link>` öğesiyle içeri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="c19ba-118">Aşağıdaki kod, `<link>` `head` etiket Yardımcısı bileşenini kullanarak öğesi öğesine `<head>` bir öğesi çıkarır:</span><span class="sxs-lookup"><span data-stu-id="c19ba-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="c19ba-119">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="c19ba-119">In the preceding code:</span></span>

* <span data-ttu-id="c19ba-120">`AddressStyleTagHelperComponent`uygular <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="c19ba-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="c19ba-121">Soyutlama:</span><span class="sxs-lookup"><span data-stu-id="c19ba-121">The abstraction:</span></span>
  * <span data-ttu-id="c19ba-122">İle sınıfının başlatılmasına izin verir <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="c19ba-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="c19ba-123">HTML öğeleri eklemek veya değiştirmek için etiket Yardımcısı bileşenlerinin kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c19ba-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="c19ba-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Özelliği, bileşenlerin işlendiği sırayı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c19ba-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="c19ba-125">`Order`bir uygulamada etiket Yardımcısı bileşenlerinin birden çok kullanımı olduğunda gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="c19ba-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>yürütme bağlamının <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> özellik değerini olarak `head`karşılaştırır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="c19ba-127">Karşılaştırma true olarak değerlendirilirse, `_style` ALANıN içeriği HTML `<head>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="c19ba-128">HTML Body öğesine Ekle</span><span class="sxs-lookup"><span data-stu-id="c19ba-128">Inject into HTML body element</span></span>

<span data-ttu-id="c19ba-129">`body` Etiket Yardımcısı bileşeni, `<script>` `<body>` öğesine bir öğesi ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="c19ba-130">Aşağıdaki kod bu tekniği göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c19ba-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="c19ba-131">`<script>` Öğesini depolamak için ayrı bir HTML dosyası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="c19ba-132">HTML dosyası, kod temizleyici ve daha sürdürülebilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="c19ba-133">Önceki kod *TagHelpers/Templates/AddressToolTipScript.html* içeriğini okur ve etiket Yardımcısı çıktısına ekler.</span><span class="sxs-lookup"><span data-stu-id="c19ba-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="c19ba-134">*Addresstooltipscript. html* dosyası aşağıdaki biçimlendirmeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="c19ba-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="c19ba-135">Yukarıdaki kod, bir [önyükleme araç ipucu pencere öğesini](https://getbootstrap.com/docs/3.3/javascript/#tooltips) bir `<address>` `printable` özniteliği içeren herhangi bir öğeye bağlar.</span><span class="sxs-lookup"><span data-stu-id="c19ba-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="c19ba-136">Bir fare işaretçisi öğenin üzerine geldiğinde efekt görünür.</span><span class="sxs-lookup"><span data-stu-id="c19ba-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="c19ba-137">Bir bileşeni kaydetme</span><span class="sxs-lookup"><span data-stu-id="c19ba-137">Register a Component</span></span>

<span data-ttu-id="c19ba-138">Uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna bir etiket Yardımcısı bileşeni eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="c19ba-139">Koleksiyona eklemenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="c19ba-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="c19ba-140">Hizmetler kapsayıcısı aracılığıyla kayıt</span><span class="sxs-lookup"><span data-stu-id="c19ba-140">Registration via services container</span></span>](#registration-via-services-container)
* <span data-ttu-id="c19ba-141">[Dosya üzerinden Razor kayıt](#registration-via-razor-file)</span><span class="sxs-lookup"><span data-stu-id="c19ba-141">[Registration via Razor file](#registration-via-razor-file)</span></span>
* [<span data-ttu-id="c19ba-142">Sayfa modeli veya denetleyici aracılığıyla kaydolma</span><span class="sxs-lookup"><span data-stu-id="c19ba-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="c19ba-143">Hizmetler kapsayıcısı aracılığıyla kayıt</span><span class="sxs-lookup"><span data-stu-id="c19ba-143">Registration via services container</span></span>

<span data-ttu-id="c19ba-144">Etiket Yardımcısı bileşen sınıfı ile <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>yönetilmemişse, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) sistemine kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="c19ba-145">Aşağıdaki `Startup.ConfigureServices` kod, `AddressStyleTagHelperComponent` ve `AddressScriptTagHelperComponent` sınıflarını geçici bir [yaşam süresine](xref:fundamentals/dependency-injection#lifetime-and-registration-options)kaydeder:</span><span class="sxs-lookup"><span data-stu-id="c19ba-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="c19ba-146">Dosya üzerinden Razor kayıt</span><span class="sxs-lookup"><span data-stu-id="c19ba-146">Registration via Razor file</span></span>

<span data-ttu-id="c19ba-147">Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir Razor sayfalar sayfasından veya bir MVC görünümünden kayıt olabilir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="c19ba-148">Bu teknik, eklenen işaretlemeyi ve bileşen yürütme sırasını bir Razor dosyadan denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="c19ba-149">`ITagHelperComponentManager`Etiket Yardımcısı bileşenleri eklemek veya uygulamadan kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="c19ba-150">Aşağıdaki kod bu tekniği ile `AddressTagHelperComponent`göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c19ba-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="c19ba-151">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="c19ba-151">In the preceding code:</span></span>

* <span data-ttu-id="c19ba-152">`@inject` Yönergesi bir örneği sağlar `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c19ba-153">Örnek, Razor dosyadaki aşağı akış erişimi için adlı `manager` bir değişkene atanır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="c19ba-154">Bir örneği `AddressTagHelperComponent` , uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="c19ba-155">`AddressTagHelperComponent`, `markup` ve `order` parametrelerini kabul eden bir oluşturucuya uyacak şekilde değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c19ba-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="c19ba-156">Belirtilen `markup` parametresi, aşağıdaki gibi ' `ProcessAsync` de kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c19ba-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="c19ba-157">Sayfa modeli veya denetleyici aracılığıyla kaydolma</span><span class="sxs-lookup"><span data-stu-id="c19ba-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="c19ba-158">Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir Razor sayfalar sayfa modelinden veya bir MVC denetleyicisinden kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="c19ba-159">Bu teknik, C# mantığını Razor dosyalardan ayırmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="c19ba-160">Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c19ba-161">Etiket Yardımcısı bileşeni, örneğin etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="c19ba-162">Aşağıdaki Razor sayfalar sayfa modelinde bu teknik gösterilmektedir `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="c19ba-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="c19ba-163">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="c19ba-163">In the preceding code:</span></span>

* <span data-ttu-id="c19ba-164">Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="c19ba-165">Bir örneği `AddressTagHelperComponent` , uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="c19ba-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="c19ba-166">Bileşen oluşturma</span><span class="sxs-lookup"><span data-stu-id="c19ba-166">Create a Component</span></span>

<span data-ttu-id="c19ba-167">Özel bir etiket Yardımcısı bileşeni oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="c19ba-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="c19ba-168">Öğesinden türeten <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>bir ortak sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c19ba-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="c19ba-169">Sınıfına bir [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) öznitelik uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c19ba-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="c19ba-170">Hedef HTML öğesinin adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="c19ba-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="c19ba-171">*Isteğe bağlı*: türün [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) IntelliSense 'de görüntülenmesini engellemek için sınıfa bir öznitelik uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c19ba-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="c19ba-172">Aşağıdaki kod, `<address>` HTML öğesini hedefleyen özel bir etiket Yardımcısı bileşeni oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c19ba-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="c19ba-173">HTML işaretlemesini aşağıdaki `address` gibi eklemek için özel etiket Yardımcısı bileşenini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c19ba-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

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

<span data-ttu-id="c19ba-174">Önceki `ProcessAsync` Yöntem, eşleşen <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> `<address>` öğeye için belirtilen HTML 'yi çıkartır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="c19ba-175">Ekleme şu durumlarda oluşur:</span><span class="sxs-lookup"><span data-stu-id="c19ba-175">The injection occurs when:</span></span>

* <span data-ttu-id="c19ba-176">Yürütme bağlamının `TagName` Özellik değeri eşittir `address`.</span><span class="sxs-lookup"><span data-stu-id="c19ba-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="c19ba-177">Karşılık gelen `<address>` öğenin bir `printable` özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="c19ba-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="c19ba-178">Örneğin, aşağıdaki `<address>` öğeyi `if` işlerken ifade true olarak değerlendirilir:</span><span class="sxs-lookup"><span data-stu-id="c19ba-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="c19ba-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c19ba-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
