---
title: ASP.NET Core'da statik varlıkları paketleyin ve küçük ASP.NET
author: scottaddie
description: Birleştirme ve kıyma tekniklerini uygulayarak ASP.NET Core web uygulamasında statik kaynakları nasıl optimize edacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658272"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="3a9c6-103">ASP.NET Core'da statik varlıkları paketleyin ve küçük ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3a9c6-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="3a9c6-104">Scott [Addie](https://twitter.com/Scott_Addie) ve [David Pine](https://twitter.com/davidpine7) tarafından</span><span class="sxs-lookup"><span data-stu-id="3a9c6-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="3a9c6-105">Bu makalede, bu özelliklerin ASP.NET Core web uygulamalarıyla nasıl kullanılabileceğini de içeren donup lama ve kıyma uygulamasının yararları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="3a9c6-106">Donma ve kıyma nedir</span><span class="sxs-lookup"><span data-stu-id="3a9c6-106">What is bundling and minification</span></span>

<span data-ttu-id="3a9c6-107">Birleştirme ve minification bir web uygulaması uygulayabilirsiniz iki farklı performans optimizasyonu vardır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="3a9c6-108">Birlikte kullanıldığında, birleştirme ve küçültme, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="3a9c6-109">Birleştirme ve minification öncelikle ilk sayfa istek yükleme süresini artırmak.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="3a9c6-110">Bir web sayfası istendikten sonra, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="3a9c6-111">Sonuç olarak, birleştirme ve mayınlama, aynı sitede aynı sayfaları veya sayfaları aynı kıymetleri talep ederken performansı artırmaz.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="3a9c6-112">Süresi dolan üstbilgi varlıklar üzerinde doğru şekilde ayarlamıyorsa ve donatma ve minification kullanılmazsa, tarayıcının tazelik buluşuşistleri birkaç gün sonra varlıkları bayatlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="3a9c6-113">Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="3a9c6-114">Bu durumda, birleştirme ve kinifikasyon ilk sayfa isteği sonra bile bir performans iyileştirmesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="3a9c6-115">Bundling</span><span class="sxs-lookup"><span data-stu-id="3a9c6-115">Bundling</span></span>

<span data-ttu-id="3a9c6-116">Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="3a9c6-117">Bundling, web sayfası gibi bir web varlığını işlemek için gereken sunucu isteklerinin sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="3a9c6-118">CSS, JavaScript, vb. için özel olarak istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya, tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="3a9c6-119">Bu, ilk sayfa yükleme performansının iyileştirilmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="3a9c6-120">Minification</span><span class="sxs-lookup"><span data-stu-id="3a9c6-120">Minification</span></span>

<span data-ttu-id="3a9c6-121">Minification işlevselliği değiştirmeden gereksiz karakterleri koddan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="3a9c6-122">Sonuç, istenen varlıklarda (CSS, resimler ve JavaScript dosyaları gibi) önemli bir boyut küçültmedir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="3a9c6-123">Minifikasyonun yaygın yan etkileri arasında değişken adlarının bir karaktere kısaltılması ve yorumların ve gereksiz beyaz boşluğun kaldırılması sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="3a9c6-124">Aşağıdaki JavaScript işlevini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="3a9c6-125">Minification aşağıdaki işlevi azaltır:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="3a9c6-126">Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra, aşağıdaki parametre ve değişken adları aşağıdaki gibi yeniden adlandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="3a9c6-127">Özgün</span><span class="sxs-lookup"><span data-stu-id="3a9c6-127">Original</span></span> | <span data-ttu-id="3a9c6-128">Yeni -den adlandır</span><span class="sxs-lookup"><span data-stu-id="3a9c6-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="3a9c6-129">Donma ve kıymanın etkisi</span><span class="sxs-lookup"><span data-stu-id="3a9c6-129">Impact of bundling and minification</span></span>

<span data-ttu-id="3a9c6-130">Aşağıdaki tablo, varlıkları tek tek yükleme ve donatve minifikasyon kullanarak arasındaki farkları sıralar:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="3a9c6-131">Eylem</span><span class="sxs-lookup"><span data-stu-id="3a9c6-131">Action</span></span> | <span data-ttu-id="3a9c6-132">B/M ile</span><span class="sxs-lookup"><span data-stu-id="3a9c6-132">With B/M</span></span> | <span data-ttu-id="3a9c6-133">B/M olmadan</span><span class="sxs-lookup"><span data-stu-id="3a9c6-133">Without B/M</span></span> | <span data-ttu-id="3a9c6-134">Değiştir</span><span class="sxs-lookup"><span data-stu-id="3a9c6-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="3a9c6-135">Dosya İstekleri</span><span class="sxs-lookup"><span data-stu-id="3a9c6-135">File Requests</span></span>  | <span data-ttu-id="3a9c6-136">7</span><span class="sxs-lookup"><span data-stu-id="3a9c6-136">7</span></span>   | <span data-ttu-id="3a9c6-137">18</span><span class="sxs-lookup"><span data-stu-id="3a9c6-137">18</span></span>     | <span data-ttu-id="3a9c6-138">157%</span><span class="sxs-lookup"><span data-stu-id="3a9c6-138">157%</span></span>
<span data-ttu-id="3a9c6-139">KB Transfer</span><span class="sxs-lookup"><span data-stu-id="3a9c6-139">KB Transferred</span></span> | <span data-ttu-id="3a9c6-140">156</span><span class="sxs-lookup"><span data-stu-id="3a9c6-140">156</span></span> | <span data-ttu-id="3a9c6-141">264.68</span><span class="sxs-lookup"><span data-stu-id="3a9c6-141">264.68</span></span> | <span data-ttu-id="3a9c6-142">%70</span><span class="sxs-lookup"><span data-stu-id="3a9c6-142">70%</span></span>
<span data-ttu-id="3a9c6-143">Yük Süresi (ms)</span><span class="sxs-lookup"><span data-stu-id="3a9c6-143">Load Time (ms)</span></span> | <span data-ttu-id="3a9c6-144">885</span><span class="sxs-lookup"><span data-stu-id="3a9c6-144">885</span></span> | <span data-ttu-id="3a9c6-145">2360</span><span class="sxs-lookup"><span data-stu-id="3a9c6-145">2360</span></span>   | <span data-ttu-id="3a9c6-146">167%</span><span class="sxs-lookup"><span data-stu-id="3a9c6-146">167%</span></span>

<span data-ttu-id="3a9c6-147">Tarayıcılar HTTP istek üstbilgiile ilgili olarak oldukça ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="3a9c6-148">Gönderilen toplam bayt, donatüründe önemli bir azalma gördü.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="3a9c6-149">Yükleme süresi önemli bir iyileşme gösterir, ancak bu örnek yerel olarak çalıştırılabilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="3a9c6-150">Bir ağ üzerinden aktarılan varlıklarla donma ve minifikasyon kullanılarak daha fazla performans artışı gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="3a9c6-151">Bir donup lama ve kıyma stratejisi seçin</span><span class="sxs-lookup"><span data-stu-id="3a9c6-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="3a9c6-152">MVC ve Razor Pages proje şablonları, JSON yapılandırma dosyasından oluşan donup ve minification için kullanıma hazır bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="3a9c6-153">[Grunt](xref:client-side/using-grunt) görev koşucusu gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşık bir şekilde gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="3a9c6-154">Geliştirme iş akışınız, linting ve görüntü optimizasyonu gibi donatma ve&mdash;kinifikasyon un ötesinde işleme gerektirdiğinde üçüncü taraf bir araç mükemmel bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="3a9c6-155">Tasarım zamanı birleştirme ve minification kullanılarak, minified dosyaları uygulamanın dağıtım önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="3a9c6-156">Dağıtımdan önce birleştirme ve kıyma, azaltılmış sunucu yükü avantajı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="3a9c6-157">Ancak, tasarım zamanı birleştirme ve minification karmaşıklık oluşturmak ve yalnızca statik dosyaları ile çalışır tanımak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="3a9c6-158">Donma ve minifikasyonu yapılandırın</span><span class="sxs-lookup"><span data-stu-id="3a9c6-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="3a9c6-159">Core 2.0 veya daha önceki ASP.NET, MVC ve Razor Pages proje şablonları her paket için seçenekleri tanımlayan bir *bundleconfig.json* yapılandırma dosyası sağlar:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3a9c6-160">ASP.NET Core 2.1 veya daha sonra, yeni bir JSON dosyası ekleyin, *bundleconfig.json*adlı , MVC veya Razor Pages proje köküne.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="3a9c6-161">Başlangıç noktası olarak bu dosyaya aşağıdaki JSON'u ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="3a9c6-162">*bundleconfig.json* dosyası her paket için seçenekleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="3a9c6-163">Önceki örnekte, özel JavaScript (*wwwroot/js/site.js)* ve stylesheet (*wwwroot/css/site.css*) dosyaları için tek bir paket yapılandırması tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="3a9c6-164">Yapılandırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-164">Configuration options include:</span></span>

* <span data-ttu-id="3a9c6-165">`outputFileName`: Çıktıya paket dosyasının adı.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="3a9c6-166">*bundleconfig.json* dosyasından göreli bir yol içerebilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="3a9c6-167">**Gerekli**</span><span class="sxs-lookup"><span data-stu-id="3a9c6-167">**required**</span></span>
* <span data-ttu-id="3a9c6-168">`inputFiles`: Birlikte paketlemek için bir dizi dosya.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="3a9c6-169">Bunlar yapılandırma dosyasına göreli yollardır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="3a9c6-170">**isteğe bağlı**, \*boş bir değer boş bir çıktı dosyasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="3a9c6-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="3a9c6-172">`minify`: Çıkış türü için minifikasyon seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="3a9c6-173">**isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="3a9c6-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="3a9c6-174">Çıktı dosyası türü başına yapılandırma seçenekleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="3a9c6-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="3a9c6-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="3a9c6-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="3a9c6-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="3a9c6-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="3a9c6-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="3a9c6-178">`includeInProject`: Oluşturulan dosyaların proje dosyasına eklenip eklenmeyeceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="3a9c6-179">**isteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="3a9c6-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="3a9c6-180">`sourceMap`: Birlikte verilen dosya için bir kaynak eşlemi oluşturup oluşturmayacağını belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="3a9c6-181">**isteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="3a9c6-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="3a9c6-182">`sourceMapRootPath`: Oluşturulan kaynak harita dosyasını depolamak için kök yolu.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="3a9c6-183">Birleştirme ve kıymanın inşa zamanı yürütmesi</span><span class="sxs-lookup"><span data-stu-id="3a9c6-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="3a9c6-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet paketi, inşa zamanında birleştirme ve kıyma nın yürütülmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="3a9c6-185">Paket, yapı ve temiz zamanda çalışan [MSBuild Hedefleri](/visualstudio/msbuild/msbuild-targets) enjekte eder.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="3a9c6-186">*Bundleconfig.json* dosyası, tanımlanan yapılandırmaya dayalı çıktı dosyalarını oluşturmak için yapı işlemi tarafından analiz edilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="3a9c6-187">BuildBundlerMinifier, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="3a9c6-188">Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a9c6-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a9c6-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3a9c6-190">*BuildBundlerMinifier* paketini projenize ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="3a9c6-191">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-191">Build the project.</span></span> <span data-ttu-id="3a9c6-192">Çıktı penceresinde aşağıdakiler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="3a9c6-193">Projeyi temizleyin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-193">Clean the project.</span></span> <span data-ttu-id="3a9c6-194">Çıktı penceresinde aşağıdakiler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="3a9c6-195">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a9c6-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3a9c6-196">*BuildBundlerMinifier* paketini projenize ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="3a9c6-197">Core 1.xASP.NET kullanıyorsanız, yeni eklenen paketi geri yükleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="3a9c6-198">Projeyi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="3a9c6-199">Aşağıdakiler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="3a9c6-200">Projeyi temizleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="3a9c6-201">Şu çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="3a9c6-202">Donuklama ve minifikasyon geçici yürütme</span><span class="sxs-lookup"><span data-stu-id="3a9c6-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="3a9c6-203">Bu birleştirme ve kıyma görevlerini, projeyi oluşturmadan özel olarak yürütmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="3a9c6-204">[BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet paketini projenize ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="3a9c6-205">BundlerMinifier.Core, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="3a9c6-206">Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="3a9c6-207">Bu paket .NET Core CLI'yi *dotnet paketi* aracını içerecek şekilde genişletir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="3a9c6-208">Aşağıdaki komut Paket Yöneticisi Konsolu (PMC) penceresinde veya komut kabuğunda yürütülebilir:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="3a9c6-209">NuGet Paket Yöneticisi \* csproj dosyasına `<PackageReference />` düğüm olarak bağımlılıkekler.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="3a9c6-210">Komut `dotnet bundle` ,.NET Core CLI'ye yalnızca `<DotNetCliToolReference />` bir düğüm kullanıldığında kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="3a9c6-211">\*.csproj dosyasını buna göre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="3a9c6-212">İş akışına dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="3a9c6-212">Add files to workflow</span></span>

<span data-ttu-id="3a9c6-213">Aşağıdakilere benzer ek bir *custom.css* dosyasının eklendiği bir örnek düşünün:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="3a9c6-214">Bir *site.min.css* dosyasına *site.css* ile minify ve bundleit için, *bundleconfig.json*göreli yolu ekleyin : *site.css*</span><span class="sxs-lookup"><span data-stu-id="3a9c6-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="3a9c6-215">Alternatif olarak, aşağıdaki globbing deseni kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="3a9c6-216">Bu globbing deseni tüm CSS dosyalarıyla eşleşir ve minified dosya deseni hariç tutar.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="3a9c6-217">Uygulamayı derleyin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-217">Build the application.</span></span> <span data-ttu-id="3a9c6-218">*Site.min.css'i* açın ve *özel.css* içeriğinin dosyanın sonuna ekolduğunu fark edin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="3a9c6-219">Çevre tabanlı donup ve minifikasyon</span><span class="sxs-lookup"><span data-stu-id="3a9c6-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="3a9c6-220">En iyi uygulama olarak, uygulamanızın paketlenmiş ve minified dosyaları bir üretim ortamında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="3a9c6-221">Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="3a9c6-222">Görünümlerinizde [Çevre Etiketi Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların eklenmeniz gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="3a9c6-223">Çevre Etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3a9c6-224">Aşağıdaki `environment` etiket, ortamda çalışırken işlenmemiş CSS dosyalarını `Development` işler:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="3a9c6-225">Aşağıdaki `environment` etiket, `Development`başka bir ortamda çalışırken birlikte verilen ve minified css dosyaları işler.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="3a9c6-226">Örneğin, bu `Production` stil `Staging` sayfalarında çalışan veya tetikler:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="3a9c6-227">Gulp gelen bundleconfig.json tüketin</span><span class="sxs-lookup"><span data-stu-id="3a9c6-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="3a9c6-228">Bir uygulamanın birleştirme ve minification iş akışının ek işleme gerektirdiği durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="3a9c6-229">Örnekler arasında görüntü optimizasyonu, önbellek busting ve CDN varlık işleme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="3a9c6-230">Bu gereksinimleri karşılamak için, birleştirme ve minification iş akışını Gulp kullanmak için dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="3a9c6-231">Bundler & Minifier uzantısını kullanma</span><span class="sxs-lookup"><span data-stu-id="3a9c6-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="3a9c6-232">Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) uzantısı Gulp'a dönüştürme işlemlerini işler.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="3a9c6-233">Bundler & Minifier uzantısı, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="3a9c6-234">Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="3a9c6-235">Solution Explorer'daki *bundleconfig.json* dosyasına sağ tıklayın ve **Bundler & Minifier** > **Convert To Gulp'u seçin...**:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Gülle bağlam ı öğesini dönüştür](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="3a9c6-237">*gulpfile.js* ve *package.json* dosyaları projeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="3a9c6-238">*package.json* dosyasının `devDependencies` bölümünde listelenen destekleyici [nPM](https://www.npmjs.com/) paketleri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="3a9c6-239">Genel bağımlılık olarak Gulp CLI yüklemek için PMC penceresinde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="3a9c6-240">*gulpfile.js* dosyası, girişler, çıktılar ve ayarlar için *bundleconfig.json* dosyasını okur.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="3a9c6-241">El ile dönüştürme</span><span class="sxs-lookup"><span data-stu-id="3a9c6-241">Convert manually</span></span>

<span data-ttu-id="3a9c6-242">Visual Studio ve/veya Bundler & Minifier uzantısı kullanılamıyorsa, el ile dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="3a9c6-243">Proje köküne aşağıdaki `devDependencies`lerle birlikte bir *package.json* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="3a9c6-244">Modül `gulp-uglify` ECMAScript (ES) 2015 / ES6 ve sonraki lerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="3a9c6-245">ES2015 / `gulp-uglify` ES6 veya daha sonra kullanmak yerine [gulp-terser'i](https://www.npmjs.com/package/gulp-terser) yükleyin.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="3a9c6-246">Aşağıdaki komutu *package.json*ile aynı düzeyde çalıştırarak bağımlılıkları yükleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="3a9c6-247">Genel bağımlılık olarak Gulp CLI yükleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="3a9c6-248">Aşağıdaki *gulpfile.js* dosyasını proje köküne kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="3a9c6-249">Gulp görevlerini çalıştır</span><span class="sxs-lookup"><span data-stu-id="3a9c6-249">Run Gulp tasks</span></span>

<span data-ttu-id="3a9c6-250">Visual Studio'da proje oluşturmadan önce Gulp minification görevini tetiklemek için \*.csproj dosyasına aşağıdaki [MSBuild Hedefini](/visualstudio/msbuild/msbuild-targets) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="3a9c6-251">Bu örnekte, önceden tanımlanmış `MyPreCompileTarget` `Build` hedeften önce hedef çalıştırılan hedef içinde tanımlanan tüm görevler.</span><span class="sxs-lookup"><span data-stu-id="3a9c6-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="3a9c6-252">Aşağıdakilere benzer çıktı Visual Studio'nun Çıkış penceresinde görünür:</span><span class="sxs-lookup"><span data-stu-id="3a9c6-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3a9c6-253">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3a9c6-253">Additional resources</span></span>

* [<span data-ttu-id="3a9c6-254">Grunt kullanma</span><span class="sxs-lookup"><span data-stu-id="3a9c6-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="3a9c6-255">Birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="3a9c6-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="3a9c6-256">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="3a9c6-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
