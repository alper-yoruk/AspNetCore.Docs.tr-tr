---
title: ASP.NET Core statik varlıkları paketleyin ve azın
author: scottaddie
description: Bir ASP.NET Core Web uygulamasındaki statik kaynakları paketleme ve küçültmeye yönelik teknikleri uygulayarak nasıl iyileştirileyeceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 84123464e8f01f8a3caa65035b3174cc04aea7cf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625862"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="ebb37-103">ASP.NET Core statik varlıkları paketleyin ve azın</span><span class="sxs-lookup"><span data-stu-id="ebb37-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="ebb37-104">[Scott Ade](https://twitter.com/Scott_Addie) ve [David çam](https://twitter.com/davidpine7) tarafından</span><span class="sxs-lookup"><span data-stu-id="ebb37-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="ebb37-105">Bu makalede, bu özelliklerin ASP.NET Core Web uygulamalarıyla nasıl kullanılabileceği da dahil olmak üzere paketleme ve küçültmeye yönelik uygulama avantajları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="ebb37-106">Paketlemeyi ve küçültmeye göre</span><span class="sxs-lookup"><span data-stu-id="ebb37-106">What is bundling and minification</span></span>

<span data-ttu-id="ebb37-107">Paketleme ve küçültme, bir Web uygulamasında uygulayabileceğiniz iki ayrı performans iyileştirmesidir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="ebb37-108">Birlikte kullanıldığında, paketleme ve minbirleştirime, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="ebb37-109">Paketleme ve minbirleştirmesi birincil olarak ilk sayfa isteği yükleme süresini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="ebb37-110">Bir Web sayfası istendiğinde, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="ebb37-111">Sonuç olarak, aynı sayfada veya sayfalarda aynı varlıkları talep eden aynı siteye veya sayfalara istekte bulunduğunda, paketleme ve minlıme performansı artırmayın.</span><span class="sxs-lookup"><span data-stu-id="ebb37-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="ebb37-112">Süre sonu üst bilgisi varlıklarda doğru ayarlanmamışsa ve paketleme ve küçültme kullanılmıyorsa, tarayıcının yenilik buluşsal yöntemleri, varlıkları birkaç günden daha eski bir süre sonra işaretler.</span><span class="sxs-lookup"><span data-stu-id="ebb37-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="ebb37-113">Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="ebb37-114">Bu durumda, paketleme ve minbirleşme, ilk sayfa isteğinden sonra bile bir performans geliştirmesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebb37-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="ebb37-115">Paketleme</span><span class="sxs-lookup"><span data-stu-id="ebb37-115">Bundling</span></span>

<span data-ttu-id="ebb37-116">Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="ebb37-117">Paketleme, Web sayfası gibi bir Web varlığını işlemek için gerekli olan sunucu isteği sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="ebb37-118">Özellikle CSS, JavaScript vb. için istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteğinin olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="ebb37-119">Bu, geliştirilmiş ilk sayfa yükü performansına neden olur.</span><span class="sxs-lookup"><span data-stu-id="ebb37-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="ebb37-120">Küçültme</span><span class="sxs-lookup"><span data-stu-id="ebb37-120">Minification</span></span>

<span data-ttu-id="ebb37-121">Minbirleşme işlevleri işlevselliği değiştirmeden koddan gereksiz karakterleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="ebb37-122">Sonuç, istenen varlıklarda (CSS, görüntüler ve JavaScript dosyaları gibi) önemli bir boyut azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="ebb37-123">Yaygın olarak kullanılan yan etkileri, değişken adlarını tek bir karaktere kısaltmayı ve açıklamaları ve gereksiz boşlukları kaldırmayı içerir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="ebb37-124">Aşağıdaki JavaScript işlevini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ebb37-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="ebb37-125">Minbirleşme işlevi şu şekilde azaltır:</span><span class="sxs-lookup"><span data-stu-id="ebb37-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="ebb37-126">Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra aşağıdaki parametre ve değişken adları şu şekilde yeniden adlandırıldı:</span><span class="sxs-lookup"><span data-stu-id="ebb37-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="ebb37-127">Özgün</span><span class="sxs-lookup"><span data-stu-id="ebb37-127">Original</span></span> | <span data-ttu-id="ebb37-128">İsim</span><span class="sxs-lookup"><span data-stu-id="ebb37-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="ebb37-129">Paketleme ve küçültmeye yönelik etkileri</span><span class="sxs-lookup"><span data-stu-id="ebb37-129">Impact of bundling and minification</span></span>

<span data-ttu-id="ebb37-130">Aşağıdaki tabloda, tek tek yükleme varlıkları ve paketleme ve küçültme kullanımı arasındaki farklılıklar özetlenmektedir:</span><span class="sxs-lookup"><span data-stu-id="ebb37-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="ebb37-131">Eylem</span><span class="sxs-lookup"><span data-stu-id="ebb37-131">Action</span></span> | <span data-ttu-id="ebb37-132">B/M ile</span><span class="sxs-lookup"><span data-stu-id="ebb37-132">With B/M</span></span> | <span data-ttu-id="ebb37-133">B/M olmadan</span><span class="sxs-lookup"><span data-stu-id="ebb37-133">Without B/M</span></span> | <span data-ttu-id="ebb37-134">Değiştir</span><span class="sxs-lookup"><span data-stu-id="ebb37-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="ebb37-135">Dosya Istekleri</span><span class="sxs-lookup"><span data-stu-id="ebb37-135">File Requests</span></span>  | <span data-ttu-id="ebb37-136">7</span><span class="sxs-lookup"><span data-stu-id="ebb37-136">7</span></span>   | <span data-ttu-id="ebb37-137">18</span><span class="sxs-lookup"><span data-stu-id="ebb37-137">18</span></span>     | <span data-ttu-id="ebb37-138">%157</span><span class="sxs-lookup"><span data-stu-id="ebb37-138">157%</span></span>
<span data-ttu-id="ebb37-139">KB aktarıldı</span><span class="sxs-lookup"><span data-stu-id="ebb37-139">KB Transferred</span></span> | <span data-ttu-id="ebb37-140">156</span><span class="sxs-lookup"><span data-stu-id="ebb37-140">156</span></span> | <span data-ttu-id="ebb37-141">264,68</span><span class="sxs-lookup"><span data-stu-id="ebb37-141">264.68</span></span> | <span data-ttu-id="ebb37-142">%70</span><span class="sxs-lookup"><span data-stu-id="ebb37-142">70%</span></span>
<span data-ttu-id="ebb37-143">Yükleme süresi (MS)</span><span class="sxs-lookup"><span data-stu-id="ebb37-143">Load Time (ms)</span></span> | <span data-ttu-id="ebb37-144">885</span><span class="sxs-lookup"><span data-stu-id="ebb37-144">885</span></span> | <span data-ttu-id="ebb37-145">2360</span><span class="sxs-lookup"><span data-stu-id="ebb37-145">2360</span></span>   | <span data-ttu-id="ebb37-146">%167</span><span class="sxs-lookup"><span data-stu-id="ebb37-146">167%</span></span>

<span data-ttu-id="ebb37-147">Tarayıcılar HTTP istek üst bilgileriyle ilgili oldukça ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="ebb37-148">Gönderilen toplam bayt ölçümü, paketleme sırasında önemli bir düşüş gördük.</span><span class="sxs-lookup"><span data-stu-id="ebb37-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="ebb37-149">Yükleme zamanı önemli bir geliştirme gösterir, ancak bu örnek yerel olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="ebb37-150">Ağ üzerinden aktarılan varlıklarla paketleme ve küçültmeye karşı kullanım sırasında daha fazla performans artışı gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="ebb37-151">Bir paketleme ve küçültmeye karşı bir strateji seçin</span><span class="sxs-lookup"><span data-stu-id="ebb37-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="ebb37-152">MVC ve Razor Pages proje şablonları, BIR JSON yapılandırma dosyasından oluşan paketleme ve küçültmeye yönelik bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebb37-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="ebb37-153">[Grdalar](xref:client-side/using-grunt) görev Çalıştırıcısı gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşıklıkla yerine getirmiş.</span><span class="sxs-lookup"><span data-stu-id="ebb37-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="ebb37-154">Geliştirme iş akışınız, bağlama ve görüntü iyileştirmesi gibi paket oluşturma ve küçültmeye karşı işleme gerektirdiğinde, üçüncü taraf bir araç harika bir araçtır &mdash; .</span><span class="sxs-lookup"><span data-stu-id="ebb37-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="ebb37-155">Tasarım zamanı paketleme ve küçültme kullanarak, küçültülmüş dosyalar uygulamanın dağıtımından önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebb37-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="ebb37-156">Dağıtımdan önce paketleme ve küçültme, azaltılmış sunucu yükünün avantajlarından faydalanabilmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebb37-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="ebb37-157">Bununla birlikte, tasarım zamanı paketleme ve küçültme, derleme karmaşıklığını artırır ve yalnızca statik dosyalarla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="ebb37-158">Paketlemeyi ve küçültmeye göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ebb37-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ebb37-159">ASP.NET Core 2,0 veya önceki sürümlerde, MVC ve Razor Sayfalar proje şablonları, her bir paket için seçenekleri tanımlayan yapılandırma dosyasında bir *bundleconfig.js* sağlar:</span><span class="sxs-lookup"><span data-stu-id="ebb37-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ebb37-160">ASP.NET Core 2,1 veya üzeri sürümlerde, MVC veya Pages proje köküne *bundleconfig.js*adlı yenı bir JSON dosyası ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="ebb37-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="ebb37-161">Aşağıdaki JSON 'yi bir başlangıç noktası olarak bu dosyaya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebb37-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="ebb37-162">Dosyadaki *bundleconfig.js* her bir paket için seçenekleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ebb37-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="ebb37-163">Yukarıdaki örnekte, özel JavaScript (*Wwwroot/js/site.js*) ve stil sayfası (*Wwwroot/CSS/site. css*) dosyaları için tek bir paket yapılandırması tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="ebb37-164">Yapılandırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="ebb37-164">Configuration options include:</span></span>

* <span data-ttu-id="ebb37-165">`outputFileName`: Çıkış yapılacak paket dosyasının adı.</span><span class="sxs-lookup"><span data-stu-id="ebb37-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="ebb37-166">Dosyadaki *bundleconfig.js* göreli bir yol içerebilir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="ebb37-167">**Gerekli**</span><span class="sxs-lookup"><span data-stu-id="ebb37-167">**required**</span></span>
* <span data-ttu-id="ebb37-168">`inputFiles`: Birlikte paketedilecek dosya dizisi.</span><span class="sxs-lookup"><span data-stu-id="ebb37-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="ebb37-169">Bunlar yapılandırma dosyasına yönelik göreli yollardır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="ebb37-170">**isteğe bağlı**\* boş bir değer boş bir çıktı dosyasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="ebb37-171">[Glob](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="ebb37-172">`minify`: Çıkış türü için minbirleşme seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="ebb37-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="ebb37-173">**isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="ebb37-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="ebb37-174">Yapılandırma seçenekleri çıkış dosyası türü başına kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="ebb37-175">CSS minifier</span><span class="sxs-lookup"><span data-stu-id="ebb37-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="ebb37-176">JavaScript minifier</span><span class="sxs-lookup"><span data-stu-id="ebb37-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="ebb37-177">HTML minifier</span><span class="sxs-lookup"><span data-stu-id="ebb37-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="ebb37-178">`includeInProject`: Oluşturulan dosyalar proje dosyasına eklenip eklenmeyeceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="ebb37-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="ebb37-179">**isteğe bağlı**, *Varsayılan-yanlış*</span><span class="sxs-lookup"><span data-stu-id="ebb37-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="ebb37-180">`sourceMap`: Paketlenmiş dosya için bir kaynak eşlemesi oluşturulup oluşturulmayacağını belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="ebb37-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="ebb37-181">**isteğe bağlı**, *Varsayılan-yanlış*</span><span class="sxs-lookup"><span data-stu-id="ebb37-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="ebb37-182">`sourceMapRootPath`: Oluşturulan kaynak eşleme dosyasını depolamak için kök yolu.</span><span class="sxs-lookup"><span data-stu-id="ebb37-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="ebb37-183">İş akışına dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="ebb37-183">Add files to workflow</span></span>

<span data-ttu-id="ebb37-184">Aşağıdakilere benzer bir ek *özel. css* dosyası eklendiğini bir örnek düşünün:</span><span class="sxs-lookup"><span data-stu-id="ebb37-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="ebb37-185">*Custom. css* dosyasını küçültmeye ve *site. css* ' yi bir *site. min. css* dosyasına paketleyip, göreli yolu *üzerinebundleconfig.js*ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebb37-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="ebb37-186">Alternatif olarak, aşağıdaki glob deseninin kullanılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="ebb37-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="ebb37-187">Bu glob model tüm CSS dosyalarıyla eşleşir ve küçültülmüş dosya modelini dışlar.</span><span class="sxs-lookup"><span data-stu-id="ebb37-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="ebb37-188">Uygulamayı derleyin.</span><span class="sxs-lookup"><span data-stu-id="ebb37-188">Build the application.</span></span> <span data-ttu-id="ebb37-189">*Site. min. css* dosyasını açın ve *Custom. css* içeriğinin dosyanın sonuna ekleneceğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="ebb37-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="ebb37-190">Ortam tabanlı paketleme ve minbirleşme</span><span class="sxs-lookup"><span data-stu-id="ebb37-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="ebb37-191">En iyi uygulama olarak, uygulamanızın paketlenmiş ve küçültülmüş dosyaları bir üretim ortamında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="ebb37-192">Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="ebb37-193">Görünümlerinizde [ortam etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların ekleneceğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="ebb37-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="ebb37-194">Ortam etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.</span><span class="sxs-lookup"><span data-stu-id="ebb37-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ebb37-195">Aşağıdaki `environment` etiket, ortamda çalışırken IŞLENMEMIŞ CSS dosyalarını işler `Development` :</span><span class="sxs-lookup"><span data-stu-id="ebb37-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="ebb37-196">Aşağıdaki `environment` etiket, dışında bir ortamda çalışırken paketlenmiş ve KÜÇÜLTÜLMÜŞ CSS dosyalarını işler `Development` .</span><span class="sxs-lookup"><span data-stu-id="ebb37-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="ebb37-197">Örneğin, içinde çalışan `Production` veya `Staging` Bu stil sayfalarını işlemeyi tetikleyen:</span><span class="sxs-lookup"><span data-stu-id="ebb37-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="ebb37-198">Gulp 'dan bundleconfig.jskullanma</span><span class="sxs-lookup"><span data-stu-id="ebb37-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="ebb37-199">Uygulamanın paketleme ve küçültmeye yönelik iş akışının ek işlem gerektirdiği durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ebb37-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="ebb37-200">Örnek görüntü iyileştirmesi, önbellek performansı ve CDN varlık işleme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebb37-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="ebb37-201">Bu gereksinimleri karşılamak için, paketleme ve küçültmeye yönelik iş akışını Gulp kullanacak şekilde dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ebb37-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="ebb37-202">Gulp kullanmak için paketleme ve küçültmeye yönelik iş akışını el ile dönüştürün</span><span class="sxs-lookup"><span data-stu-id="ebb37-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="ebb37-203">Aşağıdaki gibi, proje köküne bir dosya *package.js* ekleyin `devDependencies` :</span><span class="sxs-lookup"><span data-stu-id="ebb37-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="ebb37-204">`gulp-uglify`Modül ECMAScript (es) 2015/ES6 ve üstünü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="ebb37-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="ebb37-205">ES2015/ES6 veya üstünü kullanmak için yerine [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) 'yi yükler `gulp-uglify` .</span><span class="sxs-lookup"><span data-stu-id="ebb37-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="ebb37-206">Aşağıdaki komutu *package.js*ile aynı düzeyde çalıştırarak bağımlılıkları yükler:</span><span class="sxs-lookup"><span data-stu-id="ebb37-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="ebb37-207">Gulp CLı 'yı genel bağımlılık olarak yükler:</span><span class="sxs-lookup"><span data-stu-id="ebb37-207">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="ebb37-208">Aşağıdaki *gulpfile.js* dosyasını proje köküne kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="ebb37-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="ebb37-209">Gulp görevlerini Çalıştır</span><span class="sxs-lookup"><span data-stu-id="ebb37-209">Run Gulp tasks</span></span>

<span data-ttu-id="ebb37-210">Gulp minbirleşme görevini, proje Visual Studio 'da yapılandırmadan önce tetiklemek için:</span><span class="sxs-lookup"><span data-stu-id="ebb37-210">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="ebb37-211">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="ebb37-211">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="ebb37-212">Aşağıdaki [MSBuild hedefini](/visualstudio/msbuild/msbuild-targets) proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebb37-212">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="ebb37-213">Bu örnekte, hedef içinde tanımlanan tüm görevler `MyPreCompileTarget` önceden tanımlanmış hedeften önce çalışır `Build` .</span><span class="sxs-lookup"><span data-stu-id="ebb37-213">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="ebb37-214">Visual Studio 'nun çıkış penceresinde aşağıdakine benzer bir çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="ebb37-214">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ebb37-215">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ebb37-215">Additional resources</span></span>

* [<span data-ttu-id="ebb37-216">Grunt kullanma</span><span class="sxs-lookup"><span data-stu-id="ebb37-216">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="ebb37-217">Birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="ebb37-217">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="ebb37-218">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ebb37-218">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
