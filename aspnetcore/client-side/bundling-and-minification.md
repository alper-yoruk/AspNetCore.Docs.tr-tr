---
title: ASP.NET Core statik varlıkları paketleyin ve azın
author: scottaddie
description: Bir ASP.NET Core Web uygulamasındaki statik kaynakları paketleme ve küçültmeye yönelik teknikleri uygulayarak nasıl iyileştirileyeceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054846"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core statik varlıkları paketleyin ve azın

[Scott Ade](https://twitter.com/Scott_Addie) ve [David çam](https://twitter.com/davidpine7) tarafından

Bu makalede, bu özelliklerin ASP.NET Core Web uygulamalarıyla nasıl kullanılabileceği da dahil olmak üzere paketleme ve küçültmeye yönelik uygulama avantajları açıklanmaktadır.

## <a name="what-is-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre

Paketleme ve küçültme, bir Web uygulamasında uygulayabileceğiniz iki ayrı performans iyileştirmesidir. Birlikte kullanıldığında, paketleme ve minbirleştirime, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı geliştirir.

Paketleme ve minbirleştirmesi birincil olarak ilk sayfa isteği yükleme süresini geliştirir. Bir Web sayfası istendiğinde, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır. Sonuç olarak, aynı sayfada veya sayfalarda aynı varlıkları talep eden aynı siteye veya sayfalara istekte bulunduğunda, paketleme ve minlıme performansı artırmayın. Süre sonu üst bilgisi varlıklarda doğru ayarlanmamışsa ve paketleme ve küçültme kullanılmıyorsa, tarayıcının yenilik buluşsal yöntemleri, varlıkları birkaç günden daha eski bir süre sonra işaretler. Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir. Bu durumda, paketleme ve minbirleşme, ilk sayfa isteğinden sonra bile bir performans geliştirmesi sağlar.

### <a name="bundling"></a>Paketleme

Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir. Paketleme, Web sayfası gibi bir Web varlığını işlemek için gerekli olan sunucu isteği sayısını azaltır. Özellikle CSS, JavaScript vb. için istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteğinin olması anlamına gelir. Bu, geliştirilmiş ilk sayfa yükü performansına neden olur.

### <a name="minification"></a>Küçültme

Minbirleşme işlevleri işlevselliği değiştirmeden koddan gereksiz karakterleri kaldırır. Sonuç, istenen varlıklarda (CSS, görüntüler ve JavaScript dosyaları gibi) önemli bir boyut azalmasıyla sonuçlanır. Yaygın olarak kullanılan yan etkileri, değişken adlarını tek bir karaktere kısaltmayı ve açıklamaları ve gereksiz boşlukları kaldırmayı içerir.

Aşağıdaki JavaScript işlevini göz önünde bulundurun:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minbirleşme işlevi şu şekilde azaltır:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra aşağıdaki parametre ve değişken adları şu şekilde yeniden adlandırıldı:

Özgün | İsim
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Paketleme ve küçültmeye yönelik etkileri

Aşağıdaki tabloda, tek tek yükleme varlıkları ve paketleme ve küçültme kullanımı arasındaki farklılıklar özetlenmektedir:

Eylem | B/M ile | B/M olmadan | Değiştir
--- | :---: | :---: | :---:
Dosya Istekleri  | 7   | 18     | %157
KB aktarıldı | 156 | 264,68 | %70
Yükleme süresi (MS) | 885 | 2360   | %167

Tarayıcılar HTTP istek üst bilgileriyle ilgili oldukça ayrıntılıdır. Gönderilen toplam bayt ölçümü, paketleme sırasında önemli bir düşüş gördük. Yükleme zamanı önemli bir geliştirme gösterir, ancak bu örnek yerel olarak çalışır. Ağ üzerinden aktarılan varlıklarla paketleme ve küçültmeye karşı kullanım sırasında daha fazla performans artışı gerçekleştirilir.

## <a name="choose-a-bundling-and-minification-strategy"></a>Bir paketleme ve küçültmeye karşı bir strateji seçin

MVC ve Razor Pages proje şablonları, BIR JSON yapılandırma dosyasından oluşan paketleme ve küçültmeye yönelik bir çözüm sağlar. [Grdalar](xref:client-side/using-grunt) görev Çalıştırıcısı gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşıklıkla yerine getirmiş. Geliştirme iş akışınız, bağlama ve görüntü iyileştirmesi gibi paket oluşturma ve küçültmeye karşı işleme gerektirdiğinde, üçüncü taraf bir araç harika bir araçtır &mdash; . Tasarım zamanı paketleme ve küçültme kullanarak, küçültülmüş dosyalar uygulamanın dağıtımından önce oluşturulur. Dağıtımdan önce paketleme ve küçültme, azaltılmış sunucu yükünün avantajlarından faydalanabilmenizi sağlar. Bununla birlikte, tasarım zamanı paketleme ve küçültme, derleme karmaşıklığını artırır ve yalnızca statik dosyalarla birlikte kullanılabilir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

> [!NOTE]
> Bunun çalışması için [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet paketinin projenize eklenmesi gerekir.

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 2,0 veya önceki sürümlerde, MVC ve Razor Sayfalar proje şablonları, her bir paket için seçenekleri tanımlayan yapılandırma dosyasında bir *bundleconfig.js* sağlar:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2,1 veya üzeri sürümlerde, MVC veya Pages proje köküne *bundleconfig.js* adlı yenı bir JSON dosyası ekleyin Razor . Aşağıdaki JSON 'yi bir başlangıç noktası olarak bu dosyaya ekleyin:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Dosyadaki *bundleconfig.js* her bir paket için seçenekleri tanımlar. Yukarıdaki örnekte, özel JavaScript (*Wwwroot/js/site.js*) ve stil sayfası (*Wwwroot/CSS/site. css*) dosyaları için tek bir paket yapılandırması tanımlanmıştır.

Yapılandırma seçenekleri şunlardır:

* `outputFileName`: Çıkış yapılacak paket dosyasının adı. Dosyadaki *bundleconfig.js* göreli bir yol içerebilir. **Gerekli**
* `inputFiles`: Birlikte paketedilecek dosya dizisi. Bunlar yapılandırma dosyasına yönelik göreli yollardır. **isteğe bağlı*** boş bir değer boş bir çıktı dosyasıyla sonuçlanır. [Glob](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.
* `minify`: Çıkış türü için minbirleşme seçenekleri. **isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*
  * Yapılandırma seçenekleri çıkış dosyası türü başına kullanılabilir.
    * [CSS minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Oluşturulan dosyalar proje dosyasına eklenip eklenmeyeceğini belirten bayrak. **isteğe bağlı**, *Varsayılan-yanlış*
* `sourceMap`: Paketlenmiş dosya için bir kaynak eşlemesi oluşturulup oluşturulmayacağını belirten bayrak. **isteğe bağlı**, *Varsayılan-yanlış*
* `sourceMapRootPath`: Oluşturulan kaynak eşleme dosyasını depolamak için kök yolu.

## <a name="add-files-to-workflow"></a>İş akışına dosya ekleme

Aşağıdakilere benzer bir ek *özel. css* dosyası eklendiğini bir örnek düşünün:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

*Custom. css* dosyasını küçültmeye ve *site. css* ' yi bir *site. min. css* dosyasına paketleyip, göreli yolu *üzerinebundleconfig.js* ekleyin:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternatif olarak, aşağıdaki glob deseninin kullanılması gerekir:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Bu glob model tüm CSS dosyalarıyla eşleşir ve küçültülmüş dosya modelini dışlar.

Uygulamayı derleyin. *Site. min. css* dosyasını açın ve *Custom. css* içeriğinin dosyanın sonuna ekleneceğini unutmayın.

## <a name="environment-based-bundling-and-minification"></a>Ortam tabanlı paketleme ve minbirleşme

En iyi uygulama olarak, uygulamanızın paketlenmiş ve küçültülmüş dosyaları bir üretim ortamında kullanılmalıdır. Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını daha kolay hale getirir.

Görünümlerinizde [ortam etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların ekleneceğini belirleyin. Ortam etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.

Aşağıdaki `environment` etiket, ortamda çalışırken IŞLENMEMIŞ CSS dosyalarını işler `Development` :

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Aşağıdaki `environment` etiket, dışında bir ortamda çalışırken paketlenmiş ve KÜÇÜLTÜLMÜŞ CSS dosyalarını işler `Development` . Örneğin, içinde çalışan `Production` veya `Staging` Bu stil sayfalarını işlemeyi tetikleyen:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp 'dan bundleconfig.jskullanma

Uygulamanın paketleme ve küçültmeye yönelik iş akışının ek işlem gerektirdiği durumlar vardır. Örnek görüntü iyileştirmesi, önbellek performansı ve CDN varlık işleme sayılabilir. Bu gereksinimleri karşılamak için, paketleme ve küçültmeye yönelik iş akışını Gulp kullanacak şekilde dönüştürebilirsiniz.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Gulp kullanmak için paketleme ve küçültmeye yönelik iş akışını el ile dönüştürün

Aşağıdaki gibi, proje köküne bir dosya *package.js* ekleyin `devDependencies` :

> [!WARNING]
> `gulp-uglify`Modül ECMAScript (es) 2015/ES6 ve üstünü desteklemez. ES2015/ES6 veya üstünü kullanmak için yerine [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) 'yi yükler `gulp-uglify` .

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Aşağıdaki komutu *package.js* ile aynı düzeyde çalıştırarak bağımlılıkları yükler:

```bash
npm i
```

Gulp CLı 'yı genel bağımlılık olarak yükler:

```bash
npm i -g gulp-cli
```

Aşağıdaki *gulpfile.js* dosyasını proje köküne kopyalayın:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp görevlerini Çalıştır

Gulp minbirleşme görevini, proje Visual Studio 'da yapılandırmadan önce tetiklemek için:

1. [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet paketini yükler.
1. Aşağıdaki [MSBuild hedefini](/visualstudio/msbuild/msbuild-targets) proje dosyasına ekleyin:

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Bu örnekte, hedef içinde tanımlanan tüm görevler `MyPreCompileTarget` önceden tanımlanmış hedeften önce çalışır `Build` . Visual Studio 'nun çıkış penceresinde aşağıdakine benzer bir çıktı görüntülenir:

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

## <a name="additional-resources"></a>Ek kaynaklar

* [Grunt kullanma](xref:client-side/using-grunt)
* [Birden çok ortam kullanma](xref:fundamentals/environments)
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
