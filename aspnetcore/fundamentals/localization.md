---
title: ASP.NET Core Genelleştirme ve yerelleştirme
author: rick-anderson
description: ASP.NET Core farklı diller ve kültürlere içerik yerelleştirilmesi için nasıl hizmet ve ara yazılım sağladığını öğrenin.
ms.author: riande
ms.date: 11/30/2019
no-loc:
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
uid: fundamentals/localization
ms.openlocfilehash: fcf69bdaaed5cf0283ae27440c28061857d2cbcb
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606777"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="f11ec-103">ASP.NET Core Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f11ec-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f11ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="f11ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="f11ec-105">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="f11ec-106">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="f11ec-107">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="f11ec-108">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="f11ec-109">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="f11ec-110">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="f11ec-111">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="f11ec-112">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-112">App localization involves the following:</span></span>

1. <span data-ttu-id="f11ec-113">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="f11ec-114">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="f11ec-115">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="f11ec-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f11ec-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="f11ec-117">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-117">Make the app's content localizable</span></span>

<span data-ttu-id="f11ec-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="f11ec-119">`IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="f11ec-120">Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="f11ec-121">`IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="f11ec-122">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="f11ec-123">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="f11ec-124">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="f11ec-125">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="f11ec-126">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="f11ec-127">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="f11ec-128">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="f11ec-129">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="f11ec-130">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="f11ec-131">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="f11ec-132">`IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="f11ec-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="f11ec-133">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="f11ec-134">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-135">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f11ec-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="f11ec-136">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="f11ec-137">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="f11ec-138">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="f11ec-139">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="f11ec-140">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="f11ec-141">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="f11ec-141">View localization</span></span>

<span data-ttu-id="f11ec-142">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="f11ec-143">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="f11ec-144">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="f11ec-145">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="f11ec-146">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="f11ec-147">`ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="f11ec-148">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="f11ec-149">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="f11ec-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="f11ec-150">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="f11ec-151">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f11ec-151">Key</span></span> | <span data-ttu-id="f11ec-152">Değer</span><span class="sxs-lookup"><span data-stu-id="f11ec-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="f11ec-153">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="f11ec-154">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-155">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="f11ec-156">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="f11ec-156">DataAnnotations localization</span></span>

<span data-ttu-id="f11ec-157">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="f11ec-158">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="f11ec-159">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="f11ec-160">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="f11ec-161">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="f11ec-162">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="f11ec-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="f11ec-163">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="f11ec-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="f11ec-164">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="f11ec-165">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="f11ec-166">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="f11ec-167">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="f11ec-168">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="f11ec-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="f11ec-169">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="f11ec-170">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="f11ec-171">`SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="f11ec-172">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="f11ec-173">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="f11ec-174">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="f11ec-175">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="f11ec-176">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="f11ec-177">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="f11ec-178">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="f11ec-178">Resource files</span></span>

<span data-ttu-id="f11ec-179">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="f11ec-180">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="f11ec-181">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="f11ec-182">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="f11ec-183">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f11ec-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="f11ec-184">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

1. <span data-ttu-id="f11ec-187">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. <span data-ttu-id="f11ec-189">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   <span data-ttu-id="f11ec-191">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="f11ec-193">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-193">Resource file naming</span></span>

<span data-ttu-id="f11ec-194">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="f11ec-195">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="f11ec-196">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-197">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="f11ec-198">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="f11ec-199">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-200">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="f11ec-201">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-202">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="f11ec-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="f11ec-203">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-204">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="f11ec-205">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="f11ec-205">Resource name</span></span> | <span data-ttu-id="f11ec-206">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="f11ec-207">Kaynaklar/denetleyiciler. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="f11ec-208">Nokta</span><span class="sxs-lookup"><span data-stu-id="f11ec-208">Dot</span></span>  |
| <span data-ttu-id="f11ec-209">Kaynaklar/denetleyiciler/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="f11ec-210">Yol</span><span class="sxs-lookup"><span data-stu-id="f11ec-210">Path</span></span> |

<span data-ttu-id="f11ec-211">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="f11ec-212">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="f11ec-213">Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="f11ec-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="f11ec-214">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="f11ec-215">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="f11ec-216">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="f11ec-217">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="f11ec-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="f11ec-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="f11ec-219">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="f11ec-220">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="f11ec-221">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="f11ec-222">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="f11ec-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="f11ec-223">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-223">Localization does not work by default.</span></span>
* <span data-ttu-id="f11ec-224">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="f11ec-225">`RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="f11ec-226">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="f11ec-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="f11ec-227">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="f11ec-228">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="f11ec-228">Culture fallback behavior</span></span>

<span data-ttu-id="f11ec-229">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="f11ec-230">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="f11ec-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="f11ec-231">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="f11ec-232">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="f11ec-233">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="f11ec-234">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="f11ec-235">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="f11ec-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="f11ec-236">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="f11ec-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="f11ec-237">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="f11ec-238">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="f11ec-239">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="f11ec-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="f11ec-240">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="f11ec-241">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="f11ec-242">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="f11ec-243">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="f11ec-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="f11ec-244">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="f11ec-245">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="f11ec-246">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="f11ec-247">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="f11ec-248">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="f11ec-249">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="f11ec-249">Add other cultures</span></span>

<span data-ttu-id="f11ec-250">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="f11ec-251">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="f11ec-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="f11ec-252">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="f11ec-253">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="f11ec-254">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-254">Configure localization</span></span>

<span data-ttu-id="f11ec-255">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="f11ec-256">`AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="f11ec-257">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="f11ec-258">`AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="f11ec-259">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="f11ec-260">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="f11ec-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="f11ec-261">`AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="f11ec-262">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="f11ec-262">Localization middleware</span></span>

<span data-ttu-id="f11ec-263">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="f11ec-264">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="f11ec-265">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f11ec-266">`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="f11ec-267">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="f11ec-268">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="f11ec-269">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="f11ec-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="f11ec-270">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="f11ec-271">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="f11ec-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-273">Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> .</span><span class="sxs-lookup"><span data-stu-id="f11ec-273">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="f11ec-274">cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="f11ec-275">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="f11ec-276">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="f11ec-277">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="f11ec-278">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="f11ec-279">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="f11ec-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-281">Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="f11ec-282">`MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="f11ec-283">, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="f11ec-284">Varsayılan cookie ad `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="f11ec-285">cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f11ec-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="f11ec-286">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="f11ec-287">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="f11ec-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="f11ec-288">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="f11ec-289">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="f11ec-290">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="f11ec-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="f11ec-291">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="f11ec-292">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="f11ec-293">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="f11ec-294">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-294">Tap **Languages**.</span></span>

   ![Internet seçenekleri](localization/_static/lang.png)

1. <span data-ttu-id="f11ec-296">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="f11ec-297">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="f11ec-298">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-298">Add the language.</span></span>

1. <span data-ttu-id="f11ec-299">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="f11ec-300">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="f11ec-300">Use a custom provider</span></span>

<span data-ttu-id="f11ec-301">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="f11ec-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="f11ec-302">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="f11ec-303">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="f11ec-304">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="f11ec-305">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-305">Set the culture programmatically</span></span>

<span data-ttu-id="f11ec-306">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="f11ec-307">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="f11ec-308">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="f11ec-309">`SetLanguage`Yöntemi kültürü ayarlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="f11ec-310">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="f11ec-311">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="f11ec-312">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="f11ec-312">Model binding route data and query strings</span></span>

<span data-ttu-id="f11ec-313">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="f11ec-314">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="f11ec-314">Globalization and localization terms</span></span>

<span data-ttu-id="f11ec-315">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="f11ec-316">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="f11ec-317">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="f11ec-318">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="f11ec-319">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="f11ec-320">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="f11ec-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="f11ec-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="f11ec-322">Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span><span class="sxs-lookup"><span data-stu-id="f11ec-322">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="f11ec-323">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="f11ec-324">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="f11ec-325">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="f11ec-326">Larındaki</span><span class="sxs-lookup"><span data-stu-id="f11ec-326">Terms:</span></span>

* <span data-ttu-id="f11ec-327">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="f11ec-328">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="f11ec-329">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="f11ec-330">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="f11ec-331">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="f11ec-332">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="f11ec-332">(for example "en", "es")</span></span>
* <span data-ttu-id="f11ec-333">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="f11ec-334">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="f11ec-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="f11ec-335">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="f11ec-336">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="f11ec-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="f11ec-337">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="f11ec-338">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="f11ec-339">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="f11ec-340">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f11ec-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="f11ec-341">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="f11ec-342">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="f11ec-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="f11ec-343">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="f11ec-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f11ec-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="f11ec-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="f11ec-345">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="f11ec-346">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="f11ec-347">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="f11ec-348">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="f11ec-349">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="f11ec-350">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="f11ec-351">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="f11ec-352">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-352">App localization involves the following:</span></span>

1. <span data-ttu-id="f11ec-353">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="f11ec-354">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="f11ec-355">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="f11ec-356">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f11ec-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="f11ec-357">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-357">Make the app's content localizable</span></span>

<span data-ttu-id="f11ec-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="f11ec-359">`IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="f11ec-360">Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="f11ec-361">`IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="f11ec-362">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="f11ec-363">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="f11ec-364">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="f11ec-365">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="f11ec-366">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="f11ec-367">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="f11ec-368">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="f11ec-369">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="f11ec-370">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="f11ec-371">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="f11ec-372">`IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="f11ec-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="f11ec-373">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="f11ec-374">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-375">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f11ec-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="f11ec-376">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="f11ec-377">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="f11ec-378">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="f11ec-379">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="f11ec-380">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="f11ec-381">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="f11ec-381">View localization</span></span>

<span data-ttu-id="f11ec-382">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="f11ec-383">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="f11ec-384">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="f11ec-385">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="f11ec-386">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="f11ec-387">`ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="f11ec-388">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="f11ec-389">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="f11ec-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="f11ec-390">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="f11ec-391">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f11ec-391">Key</span></span> | <span data-ttu-id="f11ec-392">Değer</span><span class="sxs-lookup"><span data-stu-id="f11ec-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="f11ec-393">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="f11ec-394">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-395">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="f11ec-396">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="f11ec-396">DataAnnotations localization</span></span>

<span data-ttu-id="f11ec-397">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="f11ec-398">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="f11ec-399">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="f11ec-400">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="f11ec-401">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="f11ec-402">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="f11ec-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="f11ec-403">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="f11ec-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="f11ec-404">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="f11ec-405">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="f11ec-406">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="f11ec-407">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="f11ec-408">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="f11ec-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="f11ec-409">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="f11ec-410">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="f11ec-411">`SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="f11ec-412">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="f11ec-413">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="f11ec-414">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="f11ec-415">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="f11ec-416">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="f11ec-417">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="f11ec-418">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="f11ec-418">Resource files</span></span>

<span data-ttu-id="f11ec-419">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="f11ec-420">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="f11ec-421">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="f11ec-422">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="f11ec-423">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f11ec-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="f11ec-424">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

1. <span data-ttu-id="f11ec-427">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. <span data-ttu-id="f11ec-429">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   <span data-ttu-id="f11ec-431">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="f11ec-433">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-433">Resource file naming</span></span>

<span data-ttu-id="f11ec-434">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="f11ec-435">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="f11ec-436">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-437">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="f11ec-438">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="f11ec-439">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-440">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="f11ec-441">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-442">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="f11ec-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="f11ec-443">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-444">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="f11ec-445">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="f11ec-445">Resource name</span></span> | <span data-ttu-id="f11ec-446">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="f11ec-447">Kaynaklar/denetleyiciler. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="f11ec-448">Nokta</span><span class="sxs-lookup"><span data-stu-id="f11ec-448">Dot</span></span>  |
| <span data-ttu-id="f11ec-449">Kaynaklar/denetleyiciler/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="f11ec-450">Yol</span><span class="sxs-lookup"><span data-stu-id="f11ec-450">Path</span></span> |

<span data-ttu-id="f11ec-451">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="f11ec-452">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="f11ec-453">Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="f11ec-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="f11ec-454">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="f11ec-455">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="f11ec-456">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="f11ec-457">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="f11ec-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="f11ec-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="f11ec-459">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-459">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="f11ec-460">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="f11ec-461">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="f11ec-462">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="f11ec-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="f11ec-463">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-463">Localization does not work by default.</span></span>
* <span data-ttu-id="f11ec-464">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="f11ec-465">`RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="f11ec-466">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="f11ec-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="f11ec-467">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="f11ec-468">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="f11ec-468">Culture fallback behavior</span></span>

<span data-ttu-id="f11ec-469">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="f11ec-470">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="f11ec-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="f11ec-471">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="f11ec-472">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="f11ec-473">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="f11ec-474">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="f11ec-475">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="f11ec-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="f11ec-476">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="f11ec-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="f11ec-477">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="f11ec-478">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="f11ec-479">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="f11ec-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="f11ec-480">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="f11ec-481">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="f11ec-482">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="f11ec-483">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="f11ec-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="f11ec-484">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="f11ec-485">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="f11ec-486">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="f11ec-487">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="f11ec-488">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="f11ec-489">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="f11ec-489">Add other cultures</span></span>

<span data-ttu-id="f11ec-490">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="f11ec-491">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="f11ec-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="f11ec-492">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="f11ec-493">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="f11ec-494">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-494">Configure localization</span></span>

<span data-ttu-id="f11ec-495">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="f11ec-496">`AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="f11ec-497">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="f11ec-498">`AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="f11ec-499">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="f11ec-500">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="f11ec-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="f11ec-501">`AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="f11ec-502">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="f11ec-502">Localization middleware</span></span>

<span data-ttu-id="f11ec-503">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="f11ec-504">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="f11ec-505">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f11ec-506">`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="f11ec-507">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="f11ec-508">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="f11ec-509">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="f11ec-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="f11ec-510">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="f11ec-511">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="f11ec-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-513">Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> .</span><span class="sxs-lookup"><span data-stu-id="f11ec-513">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="f11ec-514">cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="f11ec-515">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="f11ec-516">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="f11ec-517">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="f11ec-518">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="f11ec-519">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="f11ec-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-521">Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="f11ec-522">`MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="f11ec-523">, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="f11ec-524">Varsayılan cookie ad `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="f11ec-525">cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f11ec-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="f11ec-526">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="f11ec-527">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="f11ec-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="f11ec-528">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="f11ec-529">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="f11ec-530">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="f11ec-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="f11ec-531">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="f11ec-532">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="f11ec-533">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="f11ec-534">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-534">Tap **Languages**.</span></span>

   ![Internet seçenekleri](localization/_static/lang.png)

1. <span data-ttu-id="f11ec-536">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="f11ec-537">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="f11ec-538">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-538">Add the language.</span></span>

1. <span data-ttu-id="f11ec-539">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="f11ec-540">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="f11ec-540">Use a custom provider</span></span>

<span data-ttu-id="f11ec-541">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="f11ec-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="f11ec-542">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="f11ec-543">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="f11ec-544">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="f11ec-545">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-545">Set the culture programmatically</span></span>

<span data-ttu-id="f11ec-546">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="f11ec-547">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="f11ec-548">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="f11ec-549">`SetLanguage`Yöntemi kültürü ayarlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="f11ec-550">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="f11ec-551">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="f11ec-552">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="f11ec-552">Model binding route data and query strings</span></span>

<span data-ttu-id="f11ec-553">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="f11ec-554">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="f11ec-554">Globalization and localization terms</span></span>

<span data-ttu-id="f11ec-555">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="f11ec-556">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="f11ec-557">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="f11ec-558">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="f11ec-559">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="f11ec-560">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="f11ec-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="f11ec-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="f11ec-562">Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span><span class="sxs-lookup"><span data-stu-id="f11ec-562">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="f11ec-563">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="f11ec-564">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="f11ec-565">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="f11ec-566">Larındaki</span><span class="sxs-lookup"><span data-stu-id="f11ec-566">Terms:</span></span>

* <span data-ttu-id="f11ec-567">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="f11ec-568">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="f11ec-569">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="f11ec-570">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="f11ec-571">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="f11ec-572">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="f11ec-572">(for example "en", "es")</span></span>
* <span data-ttu-id="f11ec-573">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="f11ec-574">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="f11ec-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="f11ec-575">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="f11ec-576">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="f11ec-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="f11ec-577">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="f11ec-578">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="f11ec-579">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="f11ec-580">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f11ec-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="f11ec-581">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="f11ec-582">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="f11ec-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="f11ec-583">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="f11ec-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="f11ec-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="f11ec-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="f11ec-585">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="f11ec-586">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="f11ec-587">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="f11ec-588">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="f11ec-589">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="f11ec-590">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="f11ec-591">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="f11ec-592">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-592">App localization involves the following:</span></span>

1. <span data-ttu-id="f11ec-593">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="f11ec-594">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="f11ec-595">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="f11ec-596">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f11ec-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="f11ec-597">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="f11ec-597">Make the app's content localizable</span></span>

<span data-ttu-id="f11ec-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="f11ec-599">`IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="f11ec-600">Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="f11ec-601">`IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="f11ec-602">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="f11ec-603">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="f11ec-604">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="f11ec-605">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="f11ec-606">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="f11ec-607">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="f11ec-608">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="f11ec-609">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="f11ec-610">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="f11ec-611">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="f11ec-612">`IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="f11ec-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="f11ec-613">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="f11ec-614">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-615">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f11ec-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="f11ec-616">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="f11ec-617">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="f11ec-618">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="f11ec-619">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="f11ec-620">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="f11ec-621">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="f11ec-621">View localization</span></span>

<span data-ttu-id="f11ec-622">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="f11ec-623">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="f11ec-624">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="f11ec-625">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="f11ec-626">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="f11ec-627">`ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="f11ec-628">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="f11ec-629">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="f11ec-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="f11ec-630">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="f11ec-631">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f11ec-631">Key</span></span> | <span data-ttu-id="f11ec-632">Değer</span><span class="sxs-lookup"><span data-stu-id="f11ec-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="f11ec-633">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="f11ec-634">Genellikle, HTML değil yalnızca metni yerelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="f11ec-635">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="f11ec-636">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="f11ec-636">DataAnnotations localization</span></span>

<span data-ttu-id="f11ec-637">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="f11ec-638">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="f11ec-639">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="f11ec-640">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="f11ec-641">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="f11ec-642">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="f11ec-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="f11ec-643">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="f11ec-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="f11ec-644">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="f11ec-645">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="f11ec-646">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="f11ec-647">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="f11ec-648">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="f11ec-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="f11ec-649">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="f11ec-650">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="f11ec-651">`SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="f11ec-652">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="f11ec-653">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="f11ec-654">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="f11ec-655">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="f11ec-656">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="f11ec-657">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="f11ec-658">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="f11ec-658">Resource files</span></span>

<span data-ttu-id="f11ec-659">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="f11ec-660">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="f11ec-661">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="f11ec-662">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="f11ec-663">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f11ec-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="f11ec-664">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

1. <span data-ttu-id="f11ec-667">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. <span data-ttu-id="f11ec-669">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   <span data-ttu-id="f11ec-671">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="f11ec-673">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-673">Resource file naming</span></span>

<span data-ttu-id="f11ec-674">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="f11ec-675">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="f11ec-676">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-677">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="f11ec-678">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="f11ec-679">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-680">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="f11ec-681">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-682">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="f11ec-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="f11ec-683">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="f11ec-684">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="f11ec-685">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="f11ec-685">Resource name</span></span> | <span data-ttu-id="f11ec-686">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="f11ec-687">Kaynaklar/denetleyiciler. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="f11ec-688">Nokta</span><span class="sxs-lookup"><span data-stu-id="f11ec-688">Dot</span></span>  |
| <span data-ttu-id="f11ec-689">Kaynaklar/denetleyiciler/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="f11ec-690">Yol</span><span class="sxs-lookup"><span data-stu-id="f11ec-690">Path</span></span> |

<span data-ttu-id="f11ec-691">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="f11ec-692">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="f11ec-693">Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="f11ec-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="f11ec-694">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="f11ec-695">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="f11ec-696">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="f11ec-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="f11ec-697">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="f11ec-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="f11ec-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="f11ec-699">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-699">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="f11ec-700">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="f11ec-701">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="f11ec-702">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="f11ec-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="f11ec-703">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-703">Localization does not work by default.</span></span>
* <span data-ttu-id="f11ec-704">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="f11ec-705">`RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="f11ec-706">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="f11ec-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="f11ec-707">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f11ec-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="f11ec-708">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="f11ec-708">Culture fallback behavior</span></span>

<span data-ttu-id="f11ec-709">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="f11ec-710">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="f11ec-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="f11ec-711">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="f11ec-712">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="f11ec-713">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="f11ec-714">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="f11ec-715">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="f11ec-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="f11ec-716">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="f11ec-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="f11ec-717">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="f11ec-718">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="f11ec-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="f11ec-719">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="f11ec-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="f11ec-720">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="f11ec-721">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="f11ec-722">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="f11ec-723">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="f11ec-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="f11ec-724">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="f11ec-725">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="f11ec-726">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="f11ec-727">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="f11ec-728">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="f11ec-729">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="f11ec-729">Add other cultures</span></span>

<span data-ttu-id="f11ec-730">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="f11ec-731">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="f11ec-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="f11ec-732">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="f11ec-733">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="f11ec-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="f11ec-734">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f11ec-734">Configure localization</span></span>

<span data-ttu-id="f11ec-735">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="f11ec-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="f11ec-736">`AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="f11ec-737">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="f11ec-738">`AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="f11ec-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="f11ec-739">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="f11ec-740">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="f11ec-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="f11ec-741">`AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="f11ec-742">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="f11ec-742">Localization middleware</span></span>

<span data-ttu-id="f11ec-743">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="f11ec-744">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="f11ec-745">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f11ec-746">`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="f11ec-747">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="f11ec-748">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="f11ec-749">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="f11ec-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="f11ec-750">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="f11ec-751">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="f11ec-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-753">Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <xref:System.Globalization.CultureInfo> .</span><span class="sxs-lookup"><span data-stu-id="f11ec-753">Some apps will use a query string to set the <xref:System.Globalization.CultureInfo>.</span></span> <span data-ttu-id="f11ec-754">cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="f11ec-755">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="f11ec-756">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="f11ec-757">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="f11ec-758">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="f11ec-759">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="f11ec-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="f11ec-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="f11ec-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="f11ec-761">Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="f11ec-762">`MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="f11ec-763">, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="f11ec-764">Varsayılan cookie ad `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="f11ec-765">cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f11ec-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="f11ec-766">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="f11ec-767">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="f11ec-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="f11ec-768">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="f11ec-769">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="f11ec-770">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="f11ec-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="f11ec-771">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="f11ec-772">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="f11ec-773">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="f11ec-774">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-774">Tap **Languages**.</span></span>

   ![Internet seçenekleri](localization/_static/lang.png)

1. <span data-ttu-id="f11ec-776">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="f11ec-777">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="f11ec-778">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-778">Add the language.</span></span>

1. <span data-ttu-id="f11ec-779">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="f11ec-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="f11ec-780">Content-Language HTTP üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="f11ec-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="f11ec-781">[Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) varlık üst bilgisi:</span><span class="sxs-lookup"><span data-stu-id="f11ec-781">The [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="f11ec-782">, Hedef kitleleri için tasarlanan dilleri tanımlamakta kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="f11ec-783">Kullanıcının kendi tercih ettiği dile göre ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="f11ec-784">Varlık üstbilgileri hem HTTP isteklerinde hem de yanıtlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="f11ec-785">`Content-Language`Üst bilgi, özelliği ayarlanarak eklenebilir `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="f11ec-786">`Content-Language`Üst bilgi ekleniyor:</span><span class="sxs-lookup"><span data-stu-id="f11ec-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="f11ec-787">Requestlocalizationara yazılımı ile üstbilgiyi ayarlamaya izin verir `Content-Language` `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="f11ec-788">Yanıt üst bilgisini açıkça ayarlama gereksinimini ortadan kaldırır `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="f11ec-789">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="f11ec-789">Use a custom provider</span></span>

<span data-ttu-id="f11ec-790">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="f11ec-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="f11ec-791">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="f11ec-792">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="f11ec-793">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f11ec-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="f11ec-794">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="f11ec-794">Set the culture programmatically</span></span>

<span data-ttu-id="f11ec-795">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="f11ec-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="f11ec-796">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="f11ec-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="f11ec-797">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="f11ec-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="f11ec-798">`SetLanguage`Yöntemi kültürü ayarlar cookie .</span><span class="sxs-lookup"><span data-stu-id="f11ec-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="f11ec-799">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="f11ec-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="f11ec-800">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="f11ec-801">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="f11ec-801">Model binding route data and query strings</span></span>

<span data-ttu-id="f11ec-802">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f11ec-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="f11ec-803">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="f11ec-803">Globalization and localization terms</span></span>

<span data-ttu-id="f11ec-804">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="f11ec-805">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="f11ec-806">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="f11ec-807">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="f11ec-808">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="f11ec-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="f11ec-809">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="f11ec-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="f11ec-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="f11ec-811">Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span><span class="sxs-lookup"><span data-stu-id="f11ec-811">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="f11ec-812">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="f11ec-813">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f11ec-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="f11ec-814">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="f11ec-815">Larındaki</span><span class="sxs-lookup"><span data-stu-id="f11ec-815">Terms:</span></span>

* <span data-ttu-id="f11ec-816">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="f11ec-817">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="f11ec-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="f11ec-818">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="f11ec-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="f11ec-819">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="f11ec-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="f11ec-820">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="f11ec-821">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="f11ec-821">(for example "en", "es")</span></span>
* <span data-ttu-id="f11ec-822">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="f11ec-823">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="f11ec-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="f11ec-824">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="f11ec-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="f11ec-825">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="f11ec-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="f11ec-826">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f11ec-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="f11ec-827">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="f11ec-828">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="f11ec-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="f11ec-829">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f11ec-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="f11ec-830">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f11ec-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="f11ec-831">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="f11ec-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="f11ec-832">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="f11ec-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
