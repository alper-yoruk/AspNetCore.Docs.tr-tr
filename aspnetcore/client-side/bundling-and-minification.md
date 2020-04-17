---
title: ASP.NET Core'da statik varlıkları paketleyin ve küçük ASP.NET
author: scottaddie
description: Birleştirme ve kıyma tekniklerini uygulayarak ASP.NET Core web uygulamasında statik kaynakları nasıl optimize edacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488695"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="ec948-103">ASP.NET Core'da statik varlıkları paketleyin ve küçük ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ec948-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="ec948-104">Scott [Addie](https://twitter.com/Scott_Addie) ve [David Pine](https://twitter.com/davidpine7) tarafından</span><span class="sxs-lookup"><span data-stu-id="ec948-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="ec948-105">Bu makalede, bu özelliklerin ASP.NET Core web uygulamalarıyla nasıl kullanılabileceğini de içeren donup lama ve kıyma uygulamasının yararları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ec948-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="ec948-106">Donma ve kıyma nedir</span><span class="sxs-lookup"><span data-stu-id="ec948-106">What is bundling and minification</span></span>

<span data-ttu-id="ec948-107">Birleştirme ve minification bir web uygulaması uygulayabilirsiniz iki farklı performans optimizasyonu vardır.</span><span class="sxs-lookup"><span data-stu-id="ec948-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="ec948-108">Birlikte kullanıldığında, birleştirme ve küçültme, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="ec948-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="ec948-109">Birleştirme ve minification öncelikle ilk sayfa istek yükleme süresini artırmak.</span><span class="sxs-lookup"><span data-stu-id="ec948-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="ec948-110">Bir web sayfası istendikten sonra, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır.</span><span class="sxs-lookup"><span data-stu-id="ec948-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="ec948-111">Sonuç olarak, birleştirme ve mayınlama, aynı sitede aynı sayfaları veya sayfaları aynı kıymetleri talep ederken performansı artırmaz.</span><span class="sxs-lookup"><span data-stu-id="ec948-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="ec948-112">Süresi dolan üstbilgi varlıklar üzerinde doğru şekilde ayarlamıyorsa ve donatma ve minification kullanılmazsa, tarayıcının tazelik buluşuşistleri birkaç gün sonra varlıkları bayatlar.</span><span class="sxs-lookup"><span data-stu-id="ec948-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="ec948-113">Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ec948-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="ec948-114">Bu durumda, birleştirme ve kinifikasyon ilk sayfa isteği sonra bile bir performans iyileştirmesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec948-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="ec948-115">Bundling</span><span class="sxs-lookup"><span data-stu-id="ec948-115">Bundling</span></span>

<span data-ttu-id="ec948-116">Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="ec948-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="ec948-117">Bundling, web sayfası gibi bir web varlığını işlemek için gereken sunucu isteklerinin sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="ec948-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="ec948-118">CSS, JavaScript, vb. için özel olarak istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya, tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ec948-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="ec948-119">Bu, ilk sayfa yükleme performansının iyileştirilmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="ec948-120">Minification</span><span class="sxs-lookup"><span data-stu-id="ec948-120">Minification</span></span>

<span data-ttu-id="ec948-121">Minification işlevselliği değiştirmeden gereksiz karakterleri koddan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ec948-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="ec948-122">Sonuç, istenen varlıklarda (CSS, resimler ve JavaScript dosyaları gibi) önemli bir boyut küçültmedir.</span><span class="sxs-lookup"><span data-stu-id="ec948-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="ec948-123">Minifikasyonun yaygın yan etkileri arasında değişken adlarının bir karaktere kısaltılması ve yorumların ve gereksiz beyaz boşluğun kaldırılması sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="ec948-124">Aşağıdaki JavaScript işlevini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ec948-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="ec948-125">Minification aşağıdaki işlevi azaltır:</span><span class="sxs-lookup"><span data-stu-id="ec948-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="ec948-126">Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra, aşağıdaki parametre ve değişken adları aşağıdaki gibi yeniden adlandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="ec948-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="ec948-127">Özgün</span><span class="sxs-lookup"><span data-stu-id="ec948-127">Original</span></span> | <span data-ttu-id="ec948-128">Yeni -den adlandır</span><span class="sxs-lookup"><span data-stu-id="ec948-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="ec948-129">Donma ve kıymanın etkisi</span><span class="sxs-lookup"><span data-stu-id="ec948-129">Impact of bundling and minification</span></span>

<span data-ttu-id="ec948-130">Aşağıdaki tablo, varlıkları tek tek yükleme ve donatve minifikasyon kullanarak arasındaki farkları sıralar:</span><span class="sxs-lookup"><span data-stu-id="ec948-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="ec948-131">Eylem</span><span class="sxs-lookup"><span data-stu-id="ec948-131">Action</span></span> | <span data-ttu-id="ec948-132">B/M ile</span><span class="sxs-lookup"><span data-stu-id="ec948-132">With B/M</span></span> | <span data-ttu-id="ec948-133">B/M olmadan</span><span class="sxs-lookup"><span data-stu-id="ec948-133">Without B/M</span></span> | <span data-ttu-id="ec948-134">Değiştir</span><span class="sxs-lookup"><span data-stu-id="ec948-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="ec948-135">Dosya İstekleri</span><span class="sxs-lookup"><span data-stu-id="ec948-135">File Requests</span></span>  | <span data-ttu-id="ec948-136">7</span><span class="sxs-lookup"><span data-stu-id="ec948-136">7</span></span>   | <span data-ttu-id="ec948-137">18</span><span class="sxs-lookup"><span data-stu-id="ec948-137">18</span></span>     | <span data-ttu-id="ec948-138">157%</span><span class="sxs-lookup"><span data-stu-id="ec948-138">157%</span></span>
<span data-ttu-id="ec948-139">KB Transfer</span><span class="sxs-lookup"><span data-stu-id="ec948-139">KB Transferred</span></span> | <span data-ttu-id="ec948-140">156</span><span class="sxs-lookup"><span data-stu-id="ec948-140">156</span></span> | <span data-ttu-id="ec948-141">264.68</span><span class="sxs-lookup"><span data-stu-id="ec948-141">264.68</span></span> | <span data-ttu-id="ec948-142">%70</span><span class="sxs-lookup"><span data-stu-id="ec948-142">70%</span></span>
<span data-ttu-id="ec948-143">Yük Süresi (ms)</span><span class="sxs-lookup"><span data-stu-id="ec948-143">Load Time (ms)</span></span> | <span data-ttu-id="ec948-144">885</span><span class="sxs-lookup"><span data-stu-id="ec948-144">885</span></span> | <span data-ttu-id="ec948-145">2360</span><span class="sxs-lookup"><span data-stu-id="ec948-145">2360</span></span>   | <span data-ttu-id="ec948-146">167%</span><span class="sxs-lookup"><span data-stu-id="ec948-146">167%</span></span>

<span data-ttu-id="ec948-147">Tarayıcılar HTTP istek üstbilgiile ilgili olarak oldukça ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="ec948-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="ec948-148">Gönderilen toplam bayt, donatüründe önemli bir azalma gördü.</span><span class="sxs-lookup"><span data-stu-id="ec948-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="ec948-149">Yükleme süresi önemli bir iyileşme gösterir, ancak bu örnek yerel olarak çalıştırılabilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ec948-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="ec948-150">Bir ağ üzerinden aktarılan varlıklarla donma ve minifikasyon kullanılarak daha fazla performans artışı gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="ec948-151">Bir donup lama ve kıyma stratejisi seçin</span><span class="sxs-lookup"><span data-stu-id="ec948-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="ec948-152">MVC ve Razor Pages proje şablonları, JSON yapılandırma dosyasından oluşan birleştirme ve kinifikasyon için bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec948-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="ec948-153">[Grunt](xref:client-side/using-grunt) görev koşucusu gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşık bir şekilde gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="ec948-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="ec948-154">Geliştirme iş akışınız, linting ve görüntü optimizasyonu gibi donatma ve&mdash;kinifikasyon un ötesinde işleme gerektirdiğinde üçüncü taraf bir araç mükemmel bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="ec948-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="ec948-155">Tasarım zamanı birleştirme ve minification kullanılarak, minified dosyaları uygulamanın dağıtım önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ec948-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="ec948-156">Dağıtımdan önce birleştirme ve kıyma, azaltılmış sunucu yükü avantajı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec948-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="ec948-157">Ancak, tasarım zamanı birleştirme ve minification karmaşıklık oluşturmak ve yalnızca statik dosyaları ile çalışır tanımak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ec948-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="ec948-158">Donma ve minifikasyonu yapılandırın</span><span class="sxs-lookup"><span data-stu-id="ec948-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ec948-159">Core 2.0 veya daha önceki ASP.NET, MVC ve Razor Pages proje şablonları her paket için seçenekleri tanımlayan bir *bundleconfig.json* yapılandırma dosyası sağlar:</span><span class="sxs-lookup"><span data-stu-id="ec948-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ec948-160">ASP.NET Core 2.1 veya daha sonra, yeni bir JSON dosyası ekleyin, *bundleconfig.json*adlı , MVC veya Razor Pages proje köküne.</span><span class="sxs-lookup"><span data-stu-id="ec948-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="ec948-161">Başlangıç noktası olarak bu dosyaya aşağıdaki JSON'u ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ec948-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="ec948-162">*bundleconfig.json* dosyası her paket için seçenekleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ec948-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="ec948-163">Önceki örnekte, özel JavaScript (*wwwroot/js/site.js)* ve stylesheet (*wwwroot/css/site.css*) dosyaları için tek bir paket yapılandırması tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ec948-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="ec948-164">Yapılandırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="ec948-164">Configuration options include:</span></span>

* <span data-ttu-id="ec948-165">`outputFileName`: Çıktıya paket dosyasının adı.</span><span class="sxs-lookup"><span data-stu-id="ec948-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="ec948-166">*bundleconfig.json* dosyasından göreli bir yol içerebilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="ec948-167">**Gerekli**</span><span class="sxs-lookup"><span data-stu-id="ec948-167">**required**</span></span>
* <span data-ttu-id="ec948-168">`inputFiles`: Birlikte paketlemek için bir dizi dosya.</span><span class="sxs-lookup"><span data-stu-id="ec948-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="ec948-169">Bunlar yapılandırma dosyasına göreli yollardır.</span><span class="sxs-lookup"><span data-stu-id="ec948-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="ec948-170">**isteğe bağlı**, \*boş bir değer boş bir çıktı dosyasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ec948-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="ec948-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ec948-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="ec948-172">`minify`: Çıkış türü için minifikasyon seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="ec948-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="ec948-173">**isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="ec948-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="ec948-174">Çıktı dosyası türü başına yapılandırma seçenekleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="ec948-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="ec948-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="ec948-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="ec948-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="ec948-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="ec948-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="ec948-178">`includeInProject`: Oluşturulan dosyaların proje dosyasına eklenip eklenmeyeceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="ec948-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="ec948-179">**isteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="ec948-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="ec948-180">`sourceMap`: Birlikte verilen dosya için bir kaynak eşlemi oluşturup oluşturmayacağını belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="ec948-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="ec948-181">**isteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="ec948-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="ec948-182">`sourceMapRootPath`: Oluşturulan kaynak harita dosyasını depolamak için kök yolu.</span><span class="sxs-lookup"><span data-stu-id="ec948-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="ec948-183">İş akışına dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="ec948-183">Add files to workflow</span></span>

<span data-ttu-id="ec948-184">Aşağıdakilere benzer ek bir *custom.css* dosyasının eklendiği bir örnek düşünün:</span><span class="sxs-lookup"><span data-stu-id="ec948-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="ec948-185">Bir *site.min.css* dosyasına *site.css* ile minify ve bundleit için, *bundleconfig.json*göreli yolu ekleyin : *site.css*</span><span class="sxs-lookup"><span data-stu-id="ec948-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="ec948-186">Alternatif olarak, aşağıdaki globbing deseni kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ec948-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="ec948-187">Bu globbing deseni tüm CSS dosyalarıyla eşleşir ve minified dosya deseni hariç tutar.</span><span class="sxs-lookup"><span data-stu-id="ec948-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="ec948-188">Uygulamayı derleyin.</span><span class="sxs-lookup"><span data-stu-id="ec948-188">Build the application.</span></span> <span data-ttu-id="ec948-189">*Site.min.css'i* açın ve *özel.css* içeriğinin dosyanın sonuna ekolduğunu fark edin.</span><span class="sxs-lookup"><span data-stu-id="ec948-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="ec948-190">Çevre tabanlı donup ve minifikasyon</span><span class="sxs-lookup"><span data-stu-id="ec948-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="ec948-191">En iyi uygulama olarak, uygulamanızın paketlenmiş ve minified dosyaları bir üretim ortamında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ec948-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="ec948-192">Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="ec948-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="ec948-193">Görünümlerinizde [Çevre Etiketi Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların eklenmeniz gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ec948-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="ec948-194">Çevre Etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.</span><span class="sxs-lookup"><span data-stu-id="ec948-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ec948-195">Aşağıdaki `environment` etiket, ortamda çalışırken işlenmemiş CSS dosyalarını `Development` işler:</span><span class="sxs-lookup"><span data-stu-id="ec948-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="ec948-196">Aşağıdaki `environment` etiket, `Development`başka bir ortamda çalışırken birlikte verilen ve minified css dosyaları işler.</span><span class="sxs-lookup"><span data-stu-id="ec948-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="ec948-197">Örneğin, bu `Production` stil `Staging` sayfalarında çalışan veya tetikler:</span><span class="sxs-lookup"><span data-stu-id="ec948-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="ec948-198">Gulp gelen bundleconfig.json tüketin</span><span class="sxs-lookup"><span data-stu-id="ec948-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="ec948-199">Bir uygulamanın birleştirme ve minification iş akışının ek işleme gerektirdiği durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ec948-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="ec948-200">Örnekler arasında görüntü optimizasyonu, önbellek busting ve CDN varlık işleme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec948-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="ec948-201">Bu gereksinimleri karşılamak için, birleştirme ve minification iş akışını Gulp kullanmak için dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ec948-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="ec948-202">Birleştirme ve kıyma iş akışını Gulp'u kullanmak için el ile dönüştürün</span><span class="sxs-lookup"><span data-stu-id="ec948-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="ec948-203">Proje köküne aşağıdaki `devDependencies`lerle birlikte bir *package.json* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ec948-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="ec948-204">Modül `gulp-uglify` ECMAScript (ES) 2015 / ES6 ve sonraki lerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="ec948-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="ec948-205">ES2015 / `gulp-uglify` ES6 veya daha sonra kullanmak yerine [gulp-terser'i](https://www.npmjs.com/package/gulp-terser) yükleyin.</span><span class="sxs-lookup"><span data-stu-id="ec948-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="ec948-206">Aşağıdaki komutu *package.json*ile aynı düzeyde çalıştırarak bağımlılıkları yükleyin:</span><span class="sxs-lookup"><span data-stu-id="ec948-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="ec948-207">Genel bağımlılık olarak Gulp CLI yükleyin:</span><span class="sxs-lookup"><span data-stu-id="ec948-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="ec948-208">Aşağıdaki *gulpfile.js* dosyasını proje köküne kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="ec948-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="ec948-209">Gulp görevlerini çalıştır</span><span class="sxs-lookup"><span data-stu-id="ec948-209">Run Gulp tasks</span></span>

<span data-ttu-id="ec948-210">Visual Studio'da proje oluşturmadan önce Gulp minification görevini tetiklemek için \*.csproj dosyasına aşağıdaki [MSBuild Hedefini](/visualstudio/msbuild/msbuild-targets) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ec948-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="ec948-211">Bu örnekte, önceden tanımlanmış `MyPreCompileTarget` `Build` hedeften önce hedef çalıştırılan hedef içinde tanımlanan tüm görevler.</span><span class="sxs-lookup"><span data-stu-id="ec948-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="ec948-212">Aşağıdakilere benzer çıktı Visual Studio'nun Çıkış penceresinde görünür:</span><span class="sxs-lookup"><span data-stu-id="ec948-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="ec948-213">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ec948-213">Additional resources</span></span>

* [<span data-ttu-id="ec948-214">Grunt kullanma</span><span class="sxs-lookup"><span data-stu-id="ec948-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="ec948-215">Birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="ec948-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="ec948-216">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ec948-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
