---
<span data-ttu-id="10ada-101">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-102">'Blazor'</span></span>
- <span data-ttu-id="10ada-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-103">'Identity'</span></span>
- <span data-ttu-id="10ada-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-105">'Razor'</span></span>
- <span data-ttu-id="10ada-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="10ada-107">ASP.NET Core Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="10ada-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="10ada-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="10ada-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="10ada-109">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="10ada-110">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="10ada-111">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="10ada-112">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="10ada-113">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="10ada-114">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="10ada-115">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="10ada-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="10ada-116">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="10ada-116">App localization involves the following:</span></span>

1. <span data-ttu-id="10ada-117">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="10ada-118">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="10ada-119">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="10ada-120">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10ada-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="10ada-121">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-121">Make the app's content localizable</span></span>

<span data-ttu-id="10ada-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `Itringlocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` ıtringlocalizer ', varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="10ada-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="10ada-123">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="10ada-124">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="10ada-125">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="10ada-126">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="10ada-127">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="10ada-128">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="10ada-129">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="10ada-130">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="10ada-131">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="10ada-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="10ada-132">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="10ada-133">`IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="10ada-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="10ada-134">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="10ada-135">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-136">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="10ada-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="10ada-137">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="10ada-138">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="10ada-139">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="10ada-140">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="10ada-141">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="10ada-142">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="10ada-142">View localization</span></span>

<span data-ttu-id="10ada-143">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="10ada-144">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="10ada-145">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="10ada-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="10ada-146">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="10ada-147">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="10ada-148">`ViewLocalizer`, kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="10ada-149">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="10ada-150">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="10ada-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="10ada-151">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="10ada-152">Anahtar</span><span class="sxs-lookup"><span data-stu-id="10ada-152">Key</span></span> | <span data-ttu-id="10ada-153">Değer</span><span class="sxs-lookup"><span data-stu-id="10ada-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="10ada-154">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-155">'Blazor'</span></span>
- <span data-ttu-id="10ada-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-156">'Identity'</span></span>
- <span data-ttu-id="10ada-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-158">'Razor'</span></span>
- <span data-ttu-id="10ada-159">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-159">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="10ada-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="10ada-161">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="10ada-162">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-163">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="10ada-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="10ada-164">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="10ada-164">DataAnnotations localization</span></span>

<span data-ttu-id="10ada-165">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="10ada-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="10ada-166">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="10ada-167">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="10ada-168">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="10ada-169">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="10ada-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="10ada-170">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="10ada-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="10ada-171">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="10ada-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="10ada-172">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="10ada-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="10ada-173">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="10ada-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="10ada-174">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="10ada-175">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="10ada-176">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="10ada-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="10ada-177">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="10ada-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="10ada-178">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="10ada-179">`SupportedCultures`Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="10ada-180">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="10ada-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="10ada-181">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="10ada-182">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="10ada-183">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="10ada-184">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="10ada-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="10ada-185">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="10ada-186">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="10ada-186">Resource files</span></span>

<span data-ttu-id="10ada-187">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="10ada-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="10ada-188">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="10ada-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="10ada-189">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="10ada-190">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="10ada-191">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10ada-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="10ada-192">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

2. <span data-ttu-id="10ada-195">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="10ada-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

3. <span data-ttu-id="10ada-197">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="10ada-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

    <span data-ttu-id="10ada-199">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="10ada-201">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-201">Resource file naming</span></span>

<span data-ttu-id="10ada-202">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="10ada-203">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="10ada-204">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-205">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="10ada-206">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="10ada-207">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-208">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="10ada-209">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-210">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="10ada-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="10ada-211">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-212">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="10ada-213">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="10ada-213">Resource name</span></span> | <span data-ttu-id="10ada-214">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="10ada-215">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-216">'Blazor'</span></span>
- <span data-ttu-id="10ada-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-217">'Identity'</span></span>
- <span data-ttu-id="10ada-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-219">'Razor'</span></span>
- <span data-ttu-id="10ada-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-221">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-222">'Blazor'</span></span>
- <span data-ttu-id="10ada-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-223">'Identity'</span></span>
- <span data-ttu-id="10ada-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-225">'Razor'</span></span>
- <span data-ttu-id="10ada-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-227">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-228">'Blazor'</span></span>
- <span data-ttu-id="10ada-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-229">'Identity'</span></span>
- <span data-ttu-id="10ada-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-231">'Razor'</span></span>
- <span data-ttu-id="10ada-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-233">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-234">'Blazor'</span></span>
- <span data-ttu-id="10ada-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-235">'Identity'</span></span>
- <span data-ttu-id="10ada-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-237">'Razor'</span></span>
- <span data-ttu-id="10ada-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-238">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-239">------   | ---title: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-240">'Blazor'</span></span>
- <span data-ttu-id="10ada-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-241">'Identity'</span></span>
- <span data-ttu-id="10ada-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-243">'Razor'</span></span>
- <span data-ttu-id="10ada-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-245">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-246">'Blazor'</span></span>
- <span data-ttu-id="10ada-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-247">'Identity'</span></span>
- <span data-ttu-id="10ada-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-249">'Razor'</span></span>
- <span data-ttu-id="10ada-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-251">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-252">'Blazor'</span></span>
- <span data-ttu-id="10ada-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-253">'Identity'</span></span>
- <span data-ttu-id="10ada-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-255">'Razor'</span></span>
- <span data-ttu-id="10ada-256">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-257">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-258">'Blazor'</span></span>
- <span data-ttu-id="10ada-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-259">'Identity'</span></span>
- <span data-ttu-id="10ada-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-261">'Razor'</span></span>
- <span data-ttu-id="10ada-262">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-262">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-263">------- | | Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta | | Kaynaklar/denetleyiciler/HomeController. fr. resx | Yol | |    |     |</span><span class="sxs-lookup"><span data-stu-id="10ada-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="10ada-264">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="10ada-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="10ada-265">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="10ada-266">kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="10ada-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="10ada-267">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="10ada-268">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="10ada-269">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="10ada-270">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="10ada-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="10ada-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="10ada-272">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="10ada-273">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="10ada-274">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="10ada-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="10ada-275">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="10ada-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="10ada-276">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-276">Localization does not work by default.</span></span>
* <span data-ttu-id="10ada-277">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="10ada-278">`RootNamespace`, yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="10ada-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="10ada-279">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="10ada-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="10ada-280">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="10ada-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="10ada-281">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="10ada-281">Culture fallback behavior</span></span>

<span data-ttu-id="10ada-282">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="10ada-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="10ada-283">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="10ada-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="10ada-284">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="10ada-285">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="10ada-286">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="10ada-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="10ada-287">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="10ada-288">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="10ada-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="10ada-289">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="10ada-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="10ada-290">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="10ada-291">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="10ada-292">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="10ada-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="10ada-293">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="10ada-294">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="10ada-295">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="10ada-296">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="10ada-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="10ada-297">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10ada-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="10ada-298">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="10ada-299">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="10ada-300">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="10ada-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="10ada-301">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="10ada-302">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="10ada-302">Add other cultures</span></span>

<span data-ttu-id="10ada-303">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="10ada-304">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="10ada-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="10ada-305">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="10ada-306">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="10ada-307">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-307">Configure localization</span></span>

<span data-ttu-id="10ada-308">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="10ada-309">`AddLocalization`Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="10ada-310">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="10ada-311">`AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="10ada-312">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="10ada-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="10ada-313">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="10ada-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="10ada-314">`AddDataAnnotationsLocalization``DataAnnotations`Soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="10ada-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="10ada-315">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="10ada-315">Localization middleware</span></span>

<span data-ttu-id="10ada-316">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="10ada-317">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="10ada-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="10ada-318">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="10ada-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="10ada-319">`UseRequestLocalization`bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="10ada-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="10ada-320">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="10ada-321">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="10ada-322">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="10ada-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="10ada-323">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="10ada-324">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="10ada-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="10ada-326">Bazı uygulamalar [kültür ve UI kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="10ada-327">Tanımlama bilgisi veya Accept-Language üst bilgisi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="10ada-328">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="10ada-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="10ada-329">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="10ada-330">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="10ada-331">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="10ada-332">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="10ada-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="10ada-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="10ada-334">Üretim uygulamaları genellikle ASP.NET Core kültür tanımlama bilgisiyle kültürü ayarlamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="10ada-335">`MakeCookieValue`Bir tanımlama bilgisi oluşturmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="10ada-336">, `CookieRequestCultureProvider` `DefaultCookieName` Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan tanımlama bilgisi adını döndürür.</span><span class="sxs-lookup"><span data-stu-id="10ada-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="10ada-337">Varsayılan tanımlama bilgisi adı `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="10ada-338">Tanımlama bilgisi biçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu, `c` `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="10ada-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="10ada-339">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="10ada-340">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="10ada-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="10ada-341">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="10ada-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="10ada-342">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="10ada-343">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="10ada-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="10ada-344">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="10ada-345">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="10ada-346">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="10ada-347">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-347">Tap **Languages**.</span></span>

    ![Internet seçenekleri](localization/_static/lang.png)

3. <span data-ttu-id="10ada-349">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="10ada-350">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="10ada-351">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-351">Add the language.</span></span>

6. <span data-ttu-id="10ada-352">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="10ada-353">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="10ada-353">Use a custom provider</span></span>

<span data-ttu-id="10ada-354">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="10ada-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="10ada-355">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="10ada-356">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="10ada-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="10ada-357">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="10ada-358">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-358">Set the culture programmatically</span></span>

<span data-ttu-id="10ada-359">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="10ada-360">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="10ada-361">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="10ada-362">`SetLanguage`Yöntemi kültür tanımlama bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="10ada-363">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="10ada-364">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="10ada-365">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="10ada-365">Model binding route data and query strings</span></span>

<span data-ttu-id="10ada-366">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="10ada-367">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="10ada-367">Globalization and localization terms</span></span>

<span data-ttu-id="10ada-368">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="10ada-369">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="10ada-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="10ada-370">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="10ada-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="10ada-371">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="10ada-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="10ada-372">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="10ada-373">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="10ada-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="10ada-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="10ada-375">Bkz. [dil kültürü adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="10ada-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="10ada-376">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="10ada-377">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="10ada-378">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="10ada-379">Larındaki</span><span class="sxs-lookup"><span data-stu-id="10ada-379">Terms:</span></span>

* <span data-ttu-id="10ada-380">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="10ada-381">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="10ada-382">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="10ada-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="10ada-383">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="10ada-384">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="10ada-385">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="10ada-385">(for example "en", "es")</span></span>
* <span data-ttu-id="10ada-386">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="10ada-387">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="10ada-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="10ada-388">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="10ada-389">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="10ada-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="10ada-390">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="10ada-391">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="10ada-392">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="10ada-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="10ada-393">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="10ada-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="10ada-394">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="10ada-395">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="10ada-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="10ada-396">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="10ada-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10ada-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="10ada-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="10ada-398">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="10ada-399">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="10ada-400">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="10ada-401">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="10ada-402">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="10ada-403">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="10ada-404">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="10ada-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="10ada-405">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="10ada-405">App localization involves the following:</span></span>

1. <span data-ttu-id="10ada-406">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="10ada-407">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="10ada-408">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="10ada-409">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10ada-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="10ada-410">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-410">Make the app's content localizable</span></span>

<span data-ttu-id="10ada-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `Itringlocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` ıtringlocalizer ', varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="10ada-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="10ada-412">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="10ada-413">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="10ada-414">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="10ada-415">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="10ada-416">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="10ada-417">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="10ada-418">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="10ada-419">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="10ada-420">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="10ada-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="10ada-421">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="10ada-422">`IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="10ada-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="10ada-423">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="10ada-424">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-425">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="10ada-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="10ada-426">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="10ada-427">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="10ada-428">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="10ada-429">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="10ada-430">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="10ada-431">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="10ada-431">View localization</span></span>

<span data-ttu-id="10ada-432">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="10ada-433">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="10ada-434">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="10ada-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="10ada-435">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="10ada-436">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="10ada-437">`ViewLocalizer`, kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="10ada-438">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="10ada-439">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="10ada-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="10ada-440">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="10ada-441">Anahtar</span><span class="sxs-lookup"><span data-stu-id="10ada-441">Key</span></span> | <span data-ttu-id="10ada-442">Değer</span><span class="sxs-lookup"><span data-stu-id="10ada-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="10ada-443">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-444">'Blazor'</span></span>
- <span data-ttu-id="10ada-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-445">'Identity'</span></span>
- <span data-ttu-id="10ada-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-447">'Razor'</span></span>
- <span data-ttu-id="10ada-448">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-448">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="10ada-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="10ada-450">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="10ada-451">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-452">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="10ada-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="10ada-453">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="10ada-453">DataAnnotations localization</span></span>

<span data-ttu-id="10ada-454">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="10ada-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="10ada-455">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="10ada-456">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="10ada-457">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="10ada-458">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="10ada-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="10ada-459">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="10ada-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="10ada-460">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="10ada-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="10ada-461">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="10ada-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="10ada-462">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="10ada-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="10ada-463">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="10ada-464">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="10ada-465">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="10ada-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="10ada-466">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="10ada-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="10ada-467">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="10ada-468">`SupportedCultures`Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="10ada-469">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="10ada-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="10ada-470">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="10ada-471">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="10ada-472">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="10ada-473">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="10ada-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="10ada-474">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="10ada-475">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="10ada-475">Resource files</span></span>

<span data-ttu-id="10ada-476">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="10ada-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="10ada-477">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="10ada-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="10ada-478">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="10ada-479">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="10ada-480">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10ada-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="10ada-481">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

2. <span data-ttu-id="10ada-484">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="10ada-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

3. <span data-ttu-id="10ada-486">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="10ada-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

    <span data-ttu-id="10ada-488">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="10ada-490">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-490">Resource file naming</span></span>

<span data-ttu-id="10ada-491">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="10ada-492">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="10ada-493">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-494">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="10ada-495">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="10ada-496">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-497">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="10ada-498">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-499">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="10ada-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="10ada-500">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-501">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="10ada-502">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="10ada-502">Resource name</span></span> | <span data-ttu-id="10ada-503">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="10ada-504">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-505">'Blazor'</span></span>
- <span data-ttu-id="10ada-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-506">'Identity'</span></span>
- <span data-ttu-id="10ada-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-508">'Razor'</span></span>
- <span data-ttu-id="10ada-509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-510">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-511">'Blazor'</span></span>
- <span data-ttu-id="10ada-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-512">'Identity'</span></span>
- <span data-ttu-id="10ada-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-514">'Razor'</span></span>
- <span data-ttu-id="10ada-515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-516">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-517">'Blazor'</span></span>
- <span data-ttu-id="10ada-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-518">'Identity'</span></span>
- <span data-ttu-id="10ada-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-520">'Razor'</span></span>
- <span data-ttu-id="10ada-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-522">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-523">'Blazor'</span></span>
- <span data-ttu-id="10ada-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-524">'Identity'</span></span>
- <span data-ttu-id="10ada-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-526">'Razor'</span></span>
- <span data-ttu-id="10ada-527">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-527">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-528">------   | ---title: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-529">'Blazor'</span></span>
- <span data-ttu-id="10ada-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-530">'Identity'</span></span>
- <span data-ttu-id="10ada-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-532">'Razor'</span></span>
- <span data-ttu-id="10ada-533">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-534">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-535">'Blazor'</span></span>
- <span data-ttu-id="10ada-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-536">'Identity'</span></span>
- <span data-ttu-id="10ada-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-538">'Razor'</span></span>
- <span data-ttu-id="10ada-539">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-540">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-541">'Blazor'</span></span>
- <span data-ttu-id="10ada-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-542">'Identity'</span></span>
- <span data-ttu-id="10ada-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-544">'Razor'</span></span>
- <span data-ttu-id="10ada-545">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-546">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-547">'Blazor'</span></span>
- <span data-ttu-id="10ada-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-548">'Identity'</span></span>
- <span data-ttu-id="10ada-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-550">'Razor'</span></span>
- <span data-ttu-id="10ada-551">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-551">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-552">------- | | Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta | | Kaynaklar/denetleyiciler/HomeController. fr. resx | Yol | |    |     |</span><span class="sxs-lookup"><span data-stu-id="10ada-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="10ada-553">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="10ada-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="10ada-554">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="10ada-555">kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="10ada-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="10ada-556">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="10ada-557">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="10ada-558">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="10ada-559">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="10ada-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="10ada-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="10ada-561">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="10ada-562">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="10ada-563">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="10ada-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="10ada-564">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="10ada-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="10ada-565">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-565">Localization does not work by default.</span></span>
* <span data-ttu-id="10ada-566">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="10ada-567">`RootNamespace`, yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="10ada-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="10ada-568">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="10ada-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="10ada-569">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="10ada-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="10ada-570">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="10ada-570">Culture fallback behavior</span></span>

<span data-ttu-id="10ada-571">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="10ada-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="10ada-572">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="10ada-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="10ada-573">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="10ada-574">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="10ada-575">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="10ada-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="10ada-576">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="10ada-577">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="10ada-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="10ada-578">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="10ada-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="10ada-579">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="10ada-580">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="10ada-581">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="10ada-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="10ada-582">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="10ada-583">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="10ada-584">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="10ada-585">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="10ada-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="10ada-586">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10ada-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="10ada-587">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="10ada-588">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="10ada-589">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="10ada-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="10ada-590">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="10ada-591">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="10ada-591">Add other cultures</span></span>

<span data-ttu-id="10ada-592">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="10ada-593">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="10ada-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="10ada-594">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="10ada-595">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="10ada-596">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-596">Configure localization</span></span>

<span data-ttu-id="10ada-597">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="10ada-598">`AddLocalization`Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="10ada-599">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="10ada-600">`AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="10ada-601">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="10ada-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="10ada-602">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="10ada-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="10ada-603">`AddDataAnnotationsLocalization``DataAnnotations`Soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="10ada-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="10ada-604">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="10ada-604">Localization middleware</span></span>

<span data-ttu-id="10ada-605">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="10ada-606">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="10ada-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="10ada-607">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="10ada-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="10ada-608">`UseRequestLocalization`bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="10ada-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="10ada-609">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="10ada-610">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="10ada-611">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="10ada-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="10ada-612">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="10ada-613">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="10ada-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="10ada-615">Bazı uygulamalar [kültür ve UI kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="10ada-616">Tanımlama bilgisi veya Accept-Language üst bilgisi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="10ada-617">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="10ada-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="10ada-618">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="10ada-619">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="10ada-620">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="10ada-621">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="10ada-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="10ada-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="10ada-623">Üretim uygulamaları genellikle ASP.NET Core kültür tanımlama bilgisiyle kültürü ayarlamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="10ada-624">`MakeCookieValue`Bir tanımlama bilgisi oluşturmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="10ada-625">, `CookieRequestCultureProvider` `DefaultCookieName` Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan tanımlama bilgisi adını döndürür.</span><span class="sxs-lookup"><span data-stu-id="10ada-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="10ada-626">Varsayılan tanımlama bilgisi adı `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="10ada-627">Tanımlama bilgisi biçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu, `c` `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="10ada-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="10ada-628">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="10ada-629">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="10ada-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="10ada-630">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="10ada-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="10ada-631">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="10ada-632">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="10ada-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="10ada-633">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="10ada-634">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="10ada-635">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="10ada-636">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-636">Tap **Languages**.</span></span>

    ![Internet seçenekleri](localization/_static/lang.png)

3. <span data-ttu-id="10ada-638">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="10ada-639">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="10ada-640">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-640">Add the language.</span></span>

6. <span data-ttu-id="10ada-641">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="10ada-642">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="10ada-642">Use a custom provider</span></span>

<span data-ttu-id="10ada-643">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="10ada-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="10ada-644">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="10ada-645">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="10ada-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="10ada-646">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="10ada-647">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-647">Set the culture programmatically</span></span>

<span data-ttu-id="10ada-648">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="10ada-649">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="10ada-650">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="10ada-651">`SetLanguage`Yöntemi kültür tanımlama bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="10ada-652">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="10ada-653">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="10ada-654">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="10ada-654">Model binding route data and query strings</span></span>

<span data-ttu-id="10ada-655">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="10ada-656">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="10ada-656">Globalization and localization terms</span></span>

<span data-ttu-id="10ada-657">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="10ada-658">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="10ada-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="10ada-659">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="10ada-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="10ada-660">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="10ada-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="10ada-661">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="10ada-662">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="10ada-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="10ada-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="10ada-664">Bkz. [dil kültürü adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="10ada-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="10ada-665">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="10ada-666">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="10ada-667">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="10ada-668">Larındaki</span><span class="sxs-lookup"><span data-stu-id="10ada-668">Terms:</span></span>

* <span data-ttu-id="10ada-669">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="10ada-670">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="10ada-671">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="10ada-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="10ada-672">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="10ada-673">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="10ada-674">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="10ada-674">(for example "en", "es")</span></span>
* <span data-ttu-id="10ada-675">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="10ada-676">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="10ada-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="10ada-677">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="10ada-678">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="10ada-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="10ada-679">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="10ada-680">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="10ada-681">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="10ada-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="10ada-682">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="10ada-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="10ada-683">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="10ada-684">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="10ada-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="10ada-685">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="10ada-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="10ada-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="10ada-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="10ada-687">Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="10ada-688">ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="10ada-689">Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="10ada-690">Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="10ada-691">Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="10ada-692">Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="10ada-693">Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.</span><span class="sxs-lookup"><span data-stu-id="10ada-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="10ada-694">Uygulama yerelleştirmesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="10ada-694">App localization involves the following:</span></span>

1. <span data-ttu-id="10ada-695">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="10ada-696">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="10ada-697">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="10ada-698">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10ada-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="10ada-699">Uygulamanın içeriğini yerelleştirilebilir yapın</span><span class="sxs-lookup"><span data-stu-id="10ada-699">Make the app's content localizable</span></span>

<span data-ttu-id="10ada-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `Itringlocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` ıtringlocalizer ', varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="10ada-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="10ada-701">Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="10ada-702">Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="10ada-703">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="10ada-704">Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="10ada-705">Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="10ada-706">Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="10ada-707">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="10ada-708">Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="10ada-709">Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="10ada-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="10ada-710">`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="10ada-711">`IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor.</span><span class="sxs-lookup"><span data-stu-id="10ada-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="10ada-712">Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="10ada-713">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-714">En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="10ada-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="10ada-715">Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="10ada-716">Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="10ada-717">Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="10ada-718">Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="10ada-719">Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="10ada-720">Yerelleştirmeyi görüntüle</span><span class="sxs-lookup"><span data-stu-id="10ada-720">View localization</span></span>

<span data-ttu-id="10ada-721">`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="10ada-722">`ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="10ada-723">Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="10ada-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="10ada-724">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="10ada-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="10ada-725">Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="10ada-726">`ViewLocalizer`, kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="10ada-727">Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="10ada-728">Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="10ada-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="10ada-729">Bir Fransızca kaynak dosyası şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="10ada-730">Anahtar</span><span class="sxs-lookup"><span data-stu-id="10ada-730">Key</span></span> | <span data-ttu-id="10ada-731">Değer</span><span class="sxs-lookup"><span data-stu-id="10ada-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="10ada-732">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-733">'Blazor'</span></span>
- <span data-ttu-id="10ada-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-734">'Identity'</span></span>
- <span data-ttu-id="10ada-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-736">'Razor'</span></span>
- <span data-ttu-id="10ada-737">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-737">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="10ada-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="10ada-739">İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="10ada-740">**Note:** Genellikle HTML değil yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="10ada-741">Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="10ada-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="10ada-742">Dataaçıklamaların yerelleştirilmesi</span><span class="sxs-lookup"><span data-stu-id="10ada-742">DataAnnotations localization</span></span>

<span data-ttu-id="10ada-743">Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="10ada-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="10ada-744">Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="10ada-745">*Resources/Viewmodeller. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="10ada-746">*Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="10ada-747">ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir.</span><span class="sxs-lookup"><span data-stu-id="10ada-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="10ada-748">ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**</span><span class="sxs-lookup"><span data-stu-id="10ada-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="10ada-749">Birden çok sınıf için bir kaynak dizesi kullanma</span><span class="sxs-lookup"><span data-stu-id="10ada-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="10ada-750">Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="10ada-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="10ada-751">Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır.</span><span class="sxs-lookup"><span data-stu-id="10ada-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="10ada-752">Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="10ada-753">Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="10ada-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="10ada-754">Supportedkültürleri ve SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="10ada-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="10ada-755">ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="10ada-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="10ada-756">İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="10ada-757">`SupportedCultures`Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="10ada-758">Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="10ada-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="10ada-759">, `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="10ada-760">`ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="10ada-761">.NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="10ada-762">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="10ada-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="10ada-763">Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="10ada-764">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="10ada-764">Resource files</span></span>

<span data-ttu-id="10ada-765">Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="10ada-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="10ada-766">Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="10ada-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="10ada-767">Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="10ada-768">"es", Ispanyolca için dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="10ada-769">Bu kaynak dosyasını Visual Studio 'da oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10ada-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="10ada-770">**Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="10ada-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır.](localization/_static/newi.png)

2. <span data-ttu-id="10ada-773">**Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="10ada-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

3. <span data-ttu-id="10ada-775">**Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="10ada-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

    <span data-ttu-id="10ada-777">Visual Studio, *Welcome. es. resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="10ada-779">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-779">Resource file naming</span></span>

<span data-ttu-id="10ada-780">Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="10ada-781">Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="10ada-782">Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-783">Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="10ada-784">Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="10ada-785">Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-786">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="10ada-787">Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-788">`ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek.</span><span class="sxs-lookup"><span data-stu-id="10ada-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="10ada-789">İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="10ada-790">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="10ada-791">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="10ada-791">Resource name</span></span> | <span data-ttu-id="10ada-792">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="10ada-793">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-794">'Blazor'</span></span>
- <span data-ttu-id="10ada-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-795">'Identity'</span></span>
- <span data-ttu-id="10ada-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-797">'Razor'</span></span>
- <span data-ttu-id="10ada-798">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-799">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-800">'Blazor'</span></span>
- <span data-ttu-id="10ada-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-801">'Identity'</span></span>
- <span data-ttu-id="10ada-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-803">'Razor'</span></span>
- <span data-ttu-id="10ada-804">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-805">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-806">'Blazor'</span></span>
- <span data-ttu-id="10ada-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-807">'Identity'</span></span>
- <span data-ttu-id="10ada-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-809">'Razor'</span></span>
- <span data-ttu-id="10ada-810">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-811">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-812">'Blazor'</span></span>
- <span data-ttu-id="10ada-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-813">'Identity'</span></span>
- <span data-ttu-id="10ada-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-815">'Razor'</span></span>
- <span data-ttu-id="10ada-816">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-816">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-817">------   | ---title: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-818">'Blazor'</span></span>
- <span data-ttu-id="10ada-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-819">'Identity'</span></span>
- <span data-ttu-id="10ada-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-821">'Razor'</span></span>
- <span data-ttu-id="10ada-822">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-823">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-824">'Blazor'</span></span>
- <span data-ttu-id="10ada-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-825">'Identity'</span></span>
- <span data-ttu-id="10ada-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-827">'Razor'</span></span>
- <span data-ttu-id="10ada-828">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-829">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-830">'Blazor'</span></span>
- <span data-ttu-id="10ada-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-831">'Identity'</span></span>
- <span data-ttu-id="10ada-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-833">'Razor'</span></span>
- <span data-ttu-id="10ada-834">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10ada-835">Başlık: Yazar: Açıklama: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="10ada-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="10ada-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10ada-836">'Blazor'</span></span>
- <span data-ttu-id="10ada-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10ada-837">'Identity'</span></span>
- <span data-ttu-id="10ada-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10ada-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="10ada-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10ada-839">'Razor'</span></span>
- <span data-ttu-id="10ada-840">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="10ada-840">'SignalR' uid:</span></span> 

<span data-ttu-id="10ada-841">------- | | Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta | | Kaynaklar/denetleyiciler/HomeController. fr. resx | Yol | |    |     |</span><span class="sxs-lookup"><span data-stu-id="10ada-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="10ada-842">Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler.</span><span class="sxs-lookup"><span data-stu-id="10ada-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="10ada-843">Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="10ada-844">kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden.</span><span class="sxs-lookup"><span data-stu-id="10ada-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="10ada-845">`ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="10ada-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="10ada-846">Kaynaklar/görünümler/giriş/about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="10ada-847">Kaynaklar/görünümler. Home. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="10ada-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="10ada-848">`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="10ada-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="10ada-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="10ada-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="10ada-850">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="10ada-851">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="10ada-852">Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` .</span><span class="sxs-lookup"><span data-stu-id="10ada-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="10ada-853">Bir derlemenin kök ad alanı, derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="10ada-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="10ada-854">Yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-854">Localization does not work by default.</span></span>
* <span data-ttu-id="10ada-855">Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="10ada-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="10ada-856">`RootNamespace`, yürütme işlemi için kullanılamayan bir derleme zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="10ada-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="10ada-857">, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):</span><span class="sxs-lookup"><span data-stu-id="10ada-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="10ada-858">Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="10ada-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="10ada-859">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="10ada-859">Culture fallback behavior</span></span>

<span data-ttu-id="10ada-860">Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="10ada-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="10ada-861">İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="10ada-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="10ada-862">Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="10ada-863">Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="10ada-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="10ada-864">Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir.</span><span class="sxs-lookup"><span data-stu-id="10ada-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="10ada-865">Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="10ada-866">Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="10ada-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="10ada-867">Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="10ada-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="10ada-868">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="10ada-869">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="10ada-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="10ada-870">*Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)</span><span class="sxs-lookup"><span data-stu-id="10ada-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="10ada-871">Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="10ada-872">Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="10ada-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="10ada-873">Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="10ada-874">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="10ada-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="10ada-875">Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10ada-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="10ada-876">ASP.NET Core, genellikle istediğiniz gibi değildir.</span><span class="sxs-lookup"><span data-stu-id="10ada-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="10ada-877">Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="10ada-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="10ada-878">*. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="10ada-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="10ada-879">Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10ada-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="10ada-880">Diğer kültürleri Ekle</span><span class="sxs-lookup"><span data-stu-id="10ada-880">Add other cultures</span></span>

<span data-ttu-id="10ada-881">Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="10ada-882">ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="10ada-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="10ada-883">Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="10ada-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="10ada-884">Her istek için dil/kültür seçmek üzere bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="10ada-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="10ada-885">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10ada-885">Configure localization</span></span>

<span data-ttu-id="10ada-886">Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="10ada-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="10ada-887">`AddLocalization`Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="10ada-888">Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="10ada-889">`AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="10ada-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="10ada-890">Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır.</span><span class="sxs-lookup"><span data-stu-id="10ada-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="10ada-891">Örneğin, *Index. fr. cshtml* dosyasındaki "fr".</span><span class="sxs-lookup"><span data-stu-id="10ada-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="10ada-892">`AddDataAnnotationsLocalization``DataAnnotations`Soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .</span><span class="sxs-lookup"><span data-stu-id="10ada-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="10ada-893">Yerelleştirme ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="10ada-893">Localization middleware</span></span>

<span data-ttu-id="10ada-894">Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="10ada-895">Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir.</span><span class="sxs-lookup"><span data-stu-id="10ada-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="10ada-896">Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .</span><span class="sxs-lookup"><span data-stu-id="10ada-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="10ada-897">`UseRequestLocalization`bir `RequestLocalizationOptions` nesnesi başlatır.</span><span class="sxs-lookup"><span data-stu-id="10ada-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="10ada-898">Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="10ada-899">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="10ada-900">Varsayılan liste, en çok belirli olan en az özel.</span><span class="sxs-lookup"><span data-stu-id="10ada-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="10ada-901">Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="10ada-902">Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="10ada-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="10ada-904">Bazı uygulamalar [kültür ve UI kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="10ada-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="10ada-905">Tanımlama bilgisi veya Accept-Language üst bilgisi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="10ada-906">Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` .</span><span class="sxs-lookup"><span data-stu-id="10ada-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="10ada-907">Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="10ada-908">Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="10ada-909">Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="10ada-910">Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="10ada-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="10ada-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="10ada-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="10ada-912">Üretim uygulamaları genellikle ASP.NET Core kültür tanımlama bilgisiyle kültürü ayarlamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="10ada-913">`MakeCookieValue`Bir tanımlama bilgisi oluşturmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="10ada-914">, `CookieRequestCultureProvider` `DefaultCookieName` Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan tanımlama bilgisi adını döndürür.</span><span class="sxs-lookup"><span data-stu-id="10ada-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="10ada-915">Varsayılan tanımlama bilgisi adı `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="10ada-916">Tanımlama bilgisi biçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu, `c` `Culture` `uic` `UICulture` Örneğin:</span><span class="sxs-lookup"><span data-stu-id="10ada-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="10ada-917">Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="10ada-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="10ada-918">Accept-Language HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="10ada-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="10ada-919">[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="10ada-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="10ada-920">Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="10ada-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="10ada-921">Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="10ada-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="10ada-922">Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="10ada-923">IE 'de Accept-Language HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="10ada-924">Dişli simgesinden **Internet seçenekleri**' ne dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="10ada-925">**Diller**' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-925">Tap **Languages**.</span></span>

    ![Internet seçenekleri](localization/_static/lang.png)

3. <span data-ttu-id="10ada-927">**Dil tercihlerini ayarla**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="10ada-928">**Dil ekle**' ye dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="10ada-929">Dilini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-929">Add the language.</span></span>

6. <span data-ttu-id="10ada-930">Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.</span><span class="sxs-lookup"><span data-stu-id="10ada-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="10ada-931">Content-Language HTTP üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="10ada-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="10ada-932">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) varlık üst bilgisi:</span><span class="sxs-lookup"><span data-stu-id="10ada-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="10ada-933">, Hedef kitleleri için tasarlanan dilleri tanımlamakta kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="10ada-934">Kullanıcının kendi tercih ettiği dile göre ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="10ada-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="10ada-935">Varlık üstbilgileri hem HTTP isteklerinde hem de yanıtlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="10ada-936">`Content-Language`Üst bilgi, özelliği ayarlanarak eklenebilir `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="10ada-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="10ada-937">`Content-Language`Üst bilgi ekleniyor:</span><span class="sxs-lookup"><span data-stu-id="10ada-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="10ada-938">Requestlocalizationara yazılımı ile üstbilgiyi ayarlamaya izin verir `Content-Language` `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="10ada-939">Yanıt üst bilgisini açıkça ayarlama gereksinimini ortadan kaldırır `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="10ada-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="10ada-940">Özel bir sağlayıcı kullan</span><span class="sxs-lookup"><span data-stu-id="10ada-940">Use a custom provider</span></span>

<span data-ttu-id="10ada-941">Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="10ada-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="10ada-942">Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="10ada-943">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="10ada-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="10ada-944">`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="10ada-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="10ada-945">Kültürü program aracılığıyla ayarlama</span><span class="sxs-lookup"><span data-stu-id="10ada-945">Set the culture programmatically</span></span>

<span data-ttu-id="10ada-946">[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` .</span><span class="sxs-lookup"><span data-stu-id="10ada-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="10ada-947">*Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="10ada-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="10ada-948">*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="10ada-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="10ada-949">`SetLanguage`Yöntemi kültür tanımlama bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10ada-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="10ada-950">Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="10ada-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="10ada-951">[GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="10ada-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="10ada-952">Model bağlama yolu verileri ve sorgu dizeleri</span><span class="sxs-lookup"><span data-stu-id="10ada-952">Model binding route data and query strings</span></span>

<span data-ttu-id="10ada-953">[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.</span><span class="sxs-lookup"><span data-stu-id="10ada-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="10ada-954">Genelleştirme ve yerelleştirme koşulları</span><span class="sxs-lookup"><span data-stu-id="10ada-954">Globalization and localization terms</span></span>

<span data-ttu-id="10ada-955">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10ada-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="10ada-956">Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="10ada-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="10ada-957">Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.</span><span class="sxs-lookup"><span data-stu-id="10ada-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="10ada-958">[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="10ada-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="10ada-959">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="10ada-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="10ada-960">Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="10ada-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="10ada-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="10ada-962">Bkz. [dil kültürü adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="10ada-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="10ada-963">Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="10ada-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="10ada-964">Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="10ada-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="10ada-965">Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="10ada-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="10ada-966">Larındaki</span><span class="sxs-lookup"><span data-stu-id="10ada-966">Terms:</span></span>

* <span data-ttu-id="10ada-967">Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="10ada-968">Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="10ada-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="10ada-969">Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="10ada-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="10ada-970">Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.</span><span class="sxs-lookup"><span data-stu-id="10ada-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="10ada-971">Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="10ada-972">(örneğin, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="10ada-972">(for example "en", "es")</span></span>
* <span data-ttu-id="10ada-973">Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="10ada-974">(örneğin, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="10ada-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="10ada-975">Üst kültür: belirli bir kültürü içeren nötr kültür.</span><span class="sxs-lookup"><span data-stu-id="10ada-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="10ada-976">(örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="10ada-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="10ada-977">Yerel ayar: bir yerel ayar kültür ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="10ada-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="10ada-978">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="10ada-979">Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .</span><span class="sxs-lookup"><span data-stu-id="10ada-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="10ada-980">.NET uygulamalarını genelleştirmek ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="10ada-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="10ada-981">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10ada-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="10ada-982">Microsoft çok dilli uygulama araç seti</span><span class="sxs-lookup"><span data-stu-id="10ada-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="10ada-983">Yerelleştirme & genel türler</span><span class="sxs-lookup"><span data-stu-id="10ada-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
