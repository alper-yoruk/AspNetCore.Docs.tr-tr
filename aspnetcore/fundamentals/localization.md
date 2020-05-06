---
title: ASP.NET Core Genelleştirme ve yerelleştirme
author: rick-anderson
description: ASP.NET Core farklı diller ve kültürlere içerik yerelleştirilmesi için nasıl hizmet ve ara yazılım sağladığını öğrenin.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 1111c0c57baf5bfd52b26b553179338100b9ba79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776571"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="87837-103">ASP.NET Core Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="87837-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="87837-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="87837-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="87837-105">ASP.NET Core bir çoklu dil web sitesi oluşturmak, sitenizin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="87837-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="87837-106">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="87837-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="87837-107">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="87837-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="87837-108">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="87837-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="87837-109">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="87837-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="87837-110">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="87837-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="87837-111">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="87837-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="87837-112">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="87837-112">App localization involves the following:</span></span>

1. <span data-ttu-id="87837-113">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="87837-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="87837-114">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="87837-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="87837-115">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="87837-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="87837-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87837-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="87837-117">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="87837-117">Make the app's content localizable</span></span>

<span data-ttu-id="87837-118">ASP.NET Core tanıtılan `IStringLocalizer` ve `IStringLocalizer<T>` yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="87837-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="87837-119">`IStringLocalizer`çalışma zamanında kültüre özgü kaynaklar sağlamak için [ResourceManager](/dotnet/api/system.resources.resourcemanager) ve [ResourceReader](/dotnet/api/system.resources.resourcereader) kullanır.</span><span class="sxs-lookup"><span data-stu-id="87837-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="87837-120">Basit arabirimde bir Dizin Oluşturucu ve yerelleştirilmiş dizeleri `IEnumerable` döndürmek için bir.</span><span class="sxs-lookup"><span data-stu-id="87837-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="87837-121">`IStringLocalizer`, varsayılan dil dizelerini bir kaynak dosyasında depolamanızı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="87837-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="87837-122">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="87837-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="87837-123">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="87837-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="87837-124">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="87837-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="87837-125">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="87837-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="87837-126">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="87837-127">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="87837-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="87837-128">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="87837-129">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="87837-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="87837-130">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="87837-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="87837-131">HTML içeren `IHtmlLocalizer<T>` kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="87837-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="87837-132">`IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="87837-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="87837-133">Aşağıda vurgulanan örnekte, yalnızca `name` PARAMETRESININ değeri HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="87837-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="87837-134">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="87837-135">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="87837-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="87837-136">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="87837-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="87837-137">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="87837-138">Örnek uygulamada, paylaşılan kaynaklar için adlı `SharedResource` bir kukla sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="87837-139">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="87837-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="87837-140">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="87837-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="87837-141">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="87837-141">View localization</span></span>

<span data-ttu-id="87837-142">Hizmet `IViewLocalizer` , bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="87837-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="87837-143">`ViewLocalizer` Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="87837-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="87837-144">Aşağıdaki kod, öğesinin `IViewLocalizer`varsayılan uygulamasının nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="87837-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="87837-145">Varsayılan uygulama, görünümün `IViewLocalizer` dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="87837-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="87837-146">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="87837-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="87837-147">`ViewLocalizer`kullanarak `IHtmlLocalizer`yorumdur 'ı uygular, bu yüzden Razor, yerelleştirilmiş dize olarak HTML kodlaması yapmaz.</span><span class="sxs-lookup"><span data-stu-id="87837-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="87837-148">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, parametreleri HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="87837-149">Aşağıdaki Razor işaretlemesini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="87837-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="87837-150">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="87837-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="87837-151">Anahtar</span><span class="sxs-lookup"><span data-stu-id="87837-151">Key</span></span> | <span data-ttu-id="87837-152">Değer</span><span class="sxs-lookup"><span data-stu-id="87837-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="87837-153">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="87837-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="87837-154">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="87837-155">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="87837-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="87837-156">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="87837-156">DataAnnotations localization</span></span>

<span data-ttu-id="87837-157">Dataek açıklamalar hata iletileri ile `IStringLocalizer<T>`yereldir.</span><span class="sxs-lookup"><span data-stu-id="87837-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="87837-158">Seçeneğini `ResourcesPath = "Resources"`kullanarak, içindeki `RegisterViewModel` hata iletileri aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="87837-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="87837-159">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="87837-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="87837-160">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="87837-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="87837-161">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="87837-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="87837-162">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="87837-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="87837-163">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="87837-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="87837-164">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="87837-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="87837-165">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="87837-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="87837-166">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource`sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="87837-167">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="87837-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="87837-168">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="87837-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="87837-169">ASP.NET Core iki kültür değeri `SupportedCultures` belirtmenize olanak tanır. `SupportedUICultures`</span><span class="sxs-lookup"><span data-stu-id="87837-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="87837-170">`SupportedCultures` İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="87837-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="87837-171">`SupportedCultures`Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="87837-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="87837-172">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="87837-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="87837-173">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="87837-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="87837-174">`ResourceManager` Yalnızca tarafından `CurrentUICulture`belirlenen kültüre özgü dizeleri arar.</span><span class="sxs-lookup"><span data-stu-id="87837-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="87837-175">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="87837-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="87837-176">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="87837-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="87837-177">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, "Perşembe, 18 `DateTime.Now.ToLongDateString()` Şubat 2016" görüntüler, ancak `CurrentCulture` "es-es" (İspanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="87837-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="87837-178">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="87837-178">Resource files</span></span>

<span data-ttu-id="87837-179">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="87837-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="87837-180">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="87837-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="87837-181">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="87837-182">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="87837-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="87837-183">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="87837-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="87837-184">**Çözüm Gezgini**' de,**Yeni öğe** **Ekle** > > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87837-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

2. <span data-ttu-id="87837-187">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87837-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

3. <span data-ttu-id="87837-189">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="87837-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

    <span data-ttu-id="87837-191">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="87837-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="87837-193">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="87837-193">Resource file naming</span></span>

<span data-ttu-id="87837-194">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="87837-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="87837-195">Örneğin, ana derlemesi sınıf `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` için olan bir projedeki bir Fransızca kaynak, *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="87837-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="87837-196">Sınıf `LocalizationWebsite.Web.Controllers.HomeController` için bir kaynak *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="87837-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="87837-197">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="87837-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="87837-198">Örneğin, örnek projede, tür `ExtraNamespace.Tools` için bir kaynak *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="87837-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="87837-199">Örnek projede, `ConfigureServices` yöntemi "resources" `ResourcesPath` olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="87837-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="87837-200">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="87837-201">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="87837-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="87837-202">`ResourcesPath` Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="87837-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="87837-203">İçin `HomeController` kaynak dosyası, *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="87837-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="87837-204">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="87837-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="87837-205">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="87837-205">Resource name</span></span> | <span data-ttu-id="87837-206">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="87837-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="87837-207">Kaynaklar/denetleyiciler. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="87837-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="87837-208">Nokta</span><span class="sxs-lookup"><span data-stu-id="87837-208">Dot</span></span>  |
| <span data-ttu-id="87837-209">Kaynaklar/denetleyiciler/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="87837-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="87837-210">Yol</span><span class="sxs-lookup"><span data-stu-id="87837-210">Path</span></span> |
|    |     |

<span data-ttu-id="87837-211">Razor Görünümlerde kullanılan `@inject IViewLocalizer` kaynak dosyaları benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="87837-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="87837-212">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="87837-212">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="87837-213">kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="87837-213"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="87837-214">"Resources" `ResourcesPath` olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="87837-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="87837-215">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="87837-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="87837-216">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="87837-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="87837-217">`ResourcesPath` Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="87837-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="87837-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="87837-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="87837-219">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="87837-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="87837-220">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="87837-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="87837-221">`my-project-name.csproj` Örneğin, bu hata için kök ad `my_project_name` alanını ve derleme adını `my-project-name` kullanır.</span><span class="sxs-lookup"><span data-stu-id="87837-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="87837-222">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="87837-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="87837-223">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="87837-223">Localization does not work by default.</span></span>
* <span data-ttu-id="87837-224">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="87837-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="87837-225">`RootNamespace`, yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="87837-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="87837-226">`RootNamespace` , Öğesinden `AssemblyName`farklıysa, *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="87837-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="87837-227">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="87837-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="87837-228">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="87837-228">Culture fallback behavior</span></span>

<span data-ttu-id="87837-229">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="87837-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="87837-230">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="87837-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="87837-231">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="87837-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="87837-232">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="87837-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="87837-233">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="87837-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="87837-234">Ana "es"&mdash;İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="87837-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="87837-235">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="87837-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="87837-236">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="87837-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="87837-237">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="87837-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="87837-238">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="87837-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="87837-239">*Welcome. resx* ("fr `NeutralResourcesLanguage` -CA" ise)</span><span class="sxs-lookup"><span data-stu-id="87837-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="87837-240">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87837-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="87837-241">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="87837-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="87837-242">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="87837-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="87837-243">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="87837-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="87837-244">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87837-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="87837-245">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="87837-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="87837-246">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="87837-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="87837-247">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="87837-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="87837-248">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="87837-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="87837-249">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="87837-249">Add other cultures</span></span>

<span data-ttu-id="87837-250">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="87837-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="87837-251">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="87837-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="87837-252">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87837-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="87837-253">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="87837-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="87837-254">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="87837-254">Configure localization</span></span>

<span data-ttu-id="87837-255">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="87837-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="87837-256">`AddLocalization`Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="87837-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="87837-257">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="87837-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="87837-258">`AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="87837-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="87837-259">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="87837-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="87837-260">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="87837-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="87837-261">`AddDataAnnotationsLocalization`Soyutlamalar aracılığıyla `IStringLocalizer` yerelleştirilmiş `DataAnnotations` doğrulama iletileri için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="87837-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="87837-262">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="87837-262">Localization middleware</span></span>

<span data-ttu-id="87837-263">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="87837-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="87837-264">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="87837-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="87837-265">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin, `app.UseMvcWithDefaultRoute()`) herhangi bir ara yazılım önce yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="87837-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="87837-266">`UseRequestLocalization`bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="87837-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="87837-267">Her istekte `RequestCultureProvider` içindeki `RequestLocalizationOptions` listesinin listesi numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="87837-268">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="87837-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="87837-269">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="87837-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="87837-270">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="87837-271">Sağlayıcıların hiçbiri istek kültürünü `DefaultRequestCulture` belirleyeiyorsa, kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="87837-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="87837-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="87837-273">Bazı uygulamalar [kültür ve UI kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="87837-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="87837-274">Tanımlama bilgisi veya Accept-Language üst bilgisi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="87837-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="87837-275">Varsayılan olarak,, `QueryStringRequestCultureProvider` `RequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="87837-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="87837-276">Sorgu dizesi parametrelerini `culture` ve `ui-culture`öğesini geçirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="87837-277">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="87837-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="87837-278">Yalnızca iki (`culture` veya `ui-culture`) birini geçirirseniz, sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="87837-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="87837-279">Örneğin, yalnızca kültür ayarlandığında, `Culture` ve ' nin her ikisi de ayarlanır `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="87837-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="87837-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="87837-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="87837-281">Üretim uygulamaları genellikle ASP.NET Core kültür tanımlama bilgisiyle kültürü ayarlamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="87837-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="87837-282">Bir tanımlama `MakeCookieValue` bilgisi oluşturmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="87837-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="87837-283">, `CookieRequestCultureProvider` `DefaultCookieName` Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan tanımlama bilgisi adını döndürür.</span><span class="sxs-lookup"><span data-stu-id="87837-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="87837-284">Varsayılan tanımlama bilgisi adı `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="87837-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="87837-285">Tanımlama `c=%LANGCODE%|uic=%LANGCODE%`bilgisi biçimi, `c` `Culture` ve `uic` `UICulture`nerede olduğu, örneğin:</span><span class="sxs-lookup"><span data-stu-id="87837-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="87837-286">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="87837-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="87837-287">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="87837-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="87837-288">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="87837-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="87837-289">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="87837-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="87837-290">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="87837-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="87837-291">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="87837-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="87837-292">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="87837-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="87837-293">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="87837-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="87837-294">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="87837-294">Tap **Languages**.</span></span>

    ![Internet seçenekleri](localization/_static/lang.png)

3. <span data-ttu-id="87837-296">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="87837-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="87837-297">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="87837-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="87837-298">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87837-298">Add the language.</span></span>

6. <span data-ttu-id="87837-299">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="87837-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="87837-300">Content-Language HTTP üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="87837-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="87837-301">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) varlık üst bilgisi:</span><span class="sxs-lookup"><span data-stu-id="87837-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="87837-302">, Hedef kitleleri için tasarlanan dilleri tanımlamakta kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="87837-303">Kullanıcının kendi tercih ettiği dile göre ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="87837-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="87837-304">Varlık üstbilgileri hem HTTP isteklerinde hem de yanıtlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87837-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="87837-305">`Content-Language` Üst bilgi, özelliği `ApplyCurrentCultureToResponseHeaders`ayarlanarak eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="87837-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="87837-306">`Content-Language` Üst bilgi ekleniyor:</span><span class="sxs-lookup"><span data-stu-id="87837-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="87837-307">Requestlocalizationara yazılımı ile `Content-Language` üstbilgiyi ayarlamaya izin verir `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="87837-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="87837-308">Yanıt üst bilgisini `Content-Language` açıkça ayarlama gereksinimini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="87837-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="87837-309">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="87837-309">Use a custom provider</span></span>

<span data-ttu-id="87837-310">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="87837-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="87837-311">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="87837-312">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="87837-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
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
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
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
::: moniker-end

<span data-ttu-id="87837-313">Yerelleştirme `RequestLocalizationOptions` sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="87837-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="87837-314">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="87837-314">Set the culture programmatically</span></span>

<span data-ttu-id="87837-315">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek `Culture` **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir.</span><span class="sxs-lookup"><span data-stu-id="87837-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="87837-316">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="87837-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="87837-317">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, düzen dosyasının `footer` bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="87837-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="87837-318">`SetLanguage` Yöntemi kültür tanımlama bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="87837-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="87837-319">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="87837-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="87837-320">GitHub 'daki **Yerelleştirme. StarterWeb** projesi [GitHub](https://github.com/aspnet/entropy) `RequestLocalizationOptions` , Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="87837-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="87837-321">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="87837-321">Model binding route data and query strings</span></span>

<span data-ttu-id="87837-322">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="87837-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="87837-323">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="87837-323">Globalization and localization terms</span></span>

<span data-ttu-id="87837-324">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="87837-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="87837-325">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="87837-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="87837-326">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="87837-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="87837-327">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="87837-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="87837-328">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>`, burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="87837-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="87837-329">Örneğin, `es-CL` Ispanyolca (Şili) Için, `en-US` İngilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="87837-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="87837-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="87837-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="87837-331">Bkz. [dil kültürü adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="87837-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="87837-332">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="87837-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="87837-333">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="87837-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="87837-334">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="87837-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="87837-335">Larındaki</span><span class="sxs-lookup"><span data-stu-id="87837-335">Terms:</span></span>

* <span data-ttu-id="87837-336">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="87837-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="87837-337">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="87837-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="87837-338">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="87837-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="87837-339">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="87837-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="87837-340">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="87837-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="87837-341">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="87837-341">(for example "en", "es")</span></span>
* <span data-ttu-id="87837-342">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="87837-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="87837-343">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="87837-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="87837-344">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="87837-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="87837-345">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="87837-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="87837-346">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="87837-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="87837-347">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="87837-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="87837-348">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="87837-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="87837-349">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="87837-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="87837-350">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="87837-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="87837-351">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="87837-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="87837-352">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="87837-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
