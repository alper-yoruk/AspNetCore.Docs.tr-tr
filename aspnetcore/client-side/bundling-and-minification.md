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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core'da statik varlıkları paketleyin ve küçük ASP.NET

Scott [Addie](https://twitter.com/Scott_Addie) ve [David Pine](https://twitter.com/davidpine7) tarafından

Bu makalede, bu özelliklerin ASP.NET Core web uygulamalarıyla nasıl kullanılabileceğini de içeren donup lama ve kıyma uygulamasının yararları açıklanmaktadır.

## <a name="what-is-bundling-and-minification"></a>Donma ve kıyma nedir

Birleştirme ve minification bir web uygulaması uygulayabilirsiniz iki farklı performans optimizasyonu vardır. Birlikte kullanıldığında, birleştirme ve küçültme, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı artırır.

Birleştirme ve minification öncelikle ilk sayfa istek yükleme süresini artırmak. Bir web sayfası istendikten sonra, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır. Sonuç olarak, birleştirme ve mayınlama, aynı sitede aynı sayfaları veya sayfaları aynı kıymetleri talep ederken performansı artırmaz. Süresi dolan üstbilgi varlıklar üzerinde doğru şekilde ayarlamıyorsa ve donatma ve minification kullanılmazsa, tarayıcının tazelik buluşuşistleri birkaç gün sonra varlıkları bayatlar. Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir. Bu durumda, birleştirme ve kinifikasyon ilk sayfa isteği sonra bile bir performans iyileştirmesi sağlar.

### <a name="bundling"></a>Bundling

Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir. Bundling, web sayfası gibi bir web varlığını işlemek için gereken sunucu isteklerinin sayısını azaltır. CSS, JavaScript, vb. için özel olarak istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya, tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteği anlamına gelir. Bu, ilk sayfa yükleme performansının iyileştirilmesine neden olabilir.

### <a name="minification"></a>Minification

Minification işlevselliği değiştirmeden gereksiz karakterleri koddan kaldırır. Sonuç, istenen varlıklarda (CSS, resimler ve JavaScript dosyaları gibi) önemli bir boyut küçültmedir. Minifikasyonun yaygın yan etkileri arasında değişken adlarının bir karaktere kısaltılması ve yorumların ve gereksiz beyaz boşluğun kaldırılması sayılabilir.

Aşağıdaki JavaScript işlevini göz önünde bulundurun:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minification aşağıdaki işlevi azaltır:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra, aşağıdaki parametre ve değişken adları aşağıdaki gibi yeniden adlandırılmıştır:

Özgün | Yeni -den adlandır
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Donma ve kıymanın etkisi

Aşağıdaki tablo, varlıkları tek tek yükleme ve donatve minifikasyon kullanarak arasındaki farkları sıralar:

Eylem | B/M ile | B/M olmadan | Değiştir
--- | :---: | :---: | :---:
Dosya İstekleri  | 7   | 18     | 157%
KB Transfer | 156 | 264.68 | %70
Yük Süresi (ms) | 885 | 2360   | 167%

Tarayıcılar HTTP istek üstbilgiile ilgili olarak oldukça ayrıntılıdır. Gönderilen toplam bayt, donatüründe önemli bir azalma gördü. Yükleme süresi önemli bir iyileşme gösterir, ancak bu örnek yerel olarak çalıştırılabilmektedir. Bir ağ üzerinden aktarılan varlıklarla donma ve minifikasyon kullanılarak daha fazla performans artışı gerçekleştirilir.

## <a name="choose-a-bundling-and-minification-strategy"></a>Bir donup lama ve kıyma stratejisi seçin

MVC ve Razor Pages proje şablonları, JSON yapılandırma dosyasından oluşan donup ve minification için kullanıma hazır bir çözüm sağlar. [Grunt](xref:client-side/using-grunt) görev koşucusu gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşık bir şekilde gerçekleştirir. Geliştirme iş akışınız, linting ve görüntü optimizasyonu gibi donatma ve&mdash;kinifikasyon un ötesinde işleme gerektirdiğinde üçüncü taraf bir araç mükemmel bir araçtır. Tasarım zamanı birleştirme ve minification kullanılarak, minified dosyaları uygulamanın dağıtım önce oluşturulur. Dağıtımdan önce birleştirme ve kıyma, azaltılmış sunucu yükü avantajı sağlar. Ancak, tasarım zamanı birleştirme ve minification karmaşıklık oluşturmak ve yalnızca statik dosyaları ile çalışır tanımak önemlidir.

## <a name="configure-bundling-and-minification"></a>Donma ve minifikasyonu yapılandırın

::: moniker range="<= aspnetcore-2.0"

Core 2.0 veya daha önceki ASP.NET, MVC ve Razor Pages proje şablonları her paket için seçenekleri tanımlayan bir *bundleconfig.json* yapılandırma dosyası sağlar:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 veya daha sonra, yeni bir JSON dosyası ekleyin, *bundleconfig.json*adlı , MVC veya Razor Pages proje köküne. Başlangıç noktası olarak bu dosyaya aşağıdaki JSON'u ekleyin:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*bundleconfig.json* dosyası her paket için seçenekleri tanımlar. Önceki örnekte, özel JavaScript (*wwwroot/js/site.js)* ve stylesheet (*wwwroot/css/site.css*) dosyaları için tek bir paket yapılandırması tanımlanır.

Yapılandırma seçenekleri şunlardır:

* `outputFileName`: Çıktıya paket dosyasının adı. *bundleconfig.json* dosyasından göreli bir yol içerebilir. **Gerekli**
* `inputFiles`: Birlikte paketlemek için bir dizi dosya. Bunlar yapılandırma dosyasına göreli yollardır. **isteğe bağlı**, *boş bir değer boş bir çıktı dosyasıyla sonuçlanır. [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.
* `minify`: Çıkış türü için minifikasyon seçenekleri. **isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*
  * Çıktı dosyası türü başına yapılandırma seçenekleri kullanılabilir.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Oluşturulan dosyaların proje dosyasına eklenip eklenmeyeceğini belirten bayrak. **isteğe bağlı**, *varsayılan - yanlış*
* `sourceMap`: Birlikte verilen dosya için bir kaynak eşlemi oluşturup oluşturmayacağını belirten bayrak. **isteğe bağlı**, *varsayılan - yanlış*
* `sourceMapRootPath`: Oluşturulan kaynak harita dosyasını depolamak için kök yolu.

## <a name="build-time-execution-of-bundling-and-minification"></a>Birleştirme ve kıymanın inşa zamanı yürütmesi

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet paketi, inşa zamanında birleştirme ve kıyma nın yürütülmesini sağlar. Paket, yapı ve temiz zamanda çalışan [MSBuild Hedefleri](/visualstudio/msbuild/msbuild-targets) enjekte eder. *Bundleconfig.json* dosyası, tanımlanan yapılandırmaya dayalı çıktı dosyalarını oluşturmak için yapı işlemi tarafından analiz edilir.

> [!NOTE]
> BuildBundlerMinifier, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir. Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*BuildBundlerMinifier* paketini projenize ekleyin.

Projeyi derleyin. Çıktı penceresinde aşağıdakiler görüntülenir:

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

Projeyi temizleyin. Çıktı penceresinde aşağıdakiler görüntülenir:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*BuildBundlerMinifier* paketini projenize ekleyin:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Core 1.xASP.NET kullanıyorsanız, yeni eklenen paketi geri yükleyin:

```dotnetcli
dotnet restore
```

Projeyi oluşturun:

```dotnetcli
dotnet build
```

Aşağıdakiler görüntülenir:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Projeyi temizleyin:

```dotnetcli
dotnet clean
```

Şu çıktı görünür:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Donuklama ve minifikasyon geçici yürütme

Bu birleştirme ve kıyma görevlerini, projeyi oluşturmadan özel olarak yürütmek mümkündür. [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet paketini projenize ekleyin:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir. Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.

Bu paket .NET Core CLI'yi *dotnet paketi* aracını içerecek şekilde genişletir. Aşağıdaki komut Paket Yöneticisi Konsolu (PMC) penceresinde veya komut kabuğunda yürütülebilir:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet Paket Yöneticisi * csproj dosyasına `<PackageReference />` düğüm olarak bağımlılıkekler. Komut `dotnet bundle` ,.NET Core CLI'ye yalnızca `<DotNetCliToolReference />` bir düğüm kullanıldığında kaydedilir. *.csproj dosyasını buna göre değiştirin.

## <a name="add-files-to-workflow"></a>İş akışına dosya ekleme

Aşağıdakilere benzer ek bir *custom.css* dosyasının eklendiği bir örnek düşünün:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Bir *site.min.css* dosyasına *site.css* ile minify ve bundleit için, *bundleconfig.json*göreli yolu ekleyin : *site.css*

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternatif olarak, aşağıdaki globbing deseni kullanılabilir:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Bu globbing deseni tüm CSS dosyalarıyla eşleşir ve minified dosya deseni hariç tutar.

Uygulamayı derleyin. *Site.min.css'i* açın ve *özel.css* içeriğinin dosyanın sonuna ekolduğunu fark edin.

## <a name="environment-based-bundling-and-minification"></a>Çevre tabanlı donup ve minifikasyon

En iyi uygulama olarak, uygulamanızın paketlenmiş ve minified dosyaları bir üretim ortamında kullanılmalıdır. Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını kolaylaştırır.

Görünümlerinizde [Çevre Etiketi Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların eklenmeniz gerektiğini belirtin. Çevre Etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.

Aşağıdaki `environment` etiket, ortamda çalışırken işlenmemiş CSS dosyalarını `Development` işler:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Aşağıdaki `environment` etiket, `Development`başka bir ortamda çalışırken birlikte verilen ve minified css dosyaları işler. Örneğin, bu `Production` stil `Staging` sayfalarında çalışan veya tetikler:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp gelen bundleconfig.json tüketin

Bir uygulamanın birleştirme ve minification iş akışının ek işleme gerektirdiği durumlar vardır. Örnekler arasında görüntü optimizasyonu, önbellek busting ve CDN varlık işleme sayılabilir. Bu gereksinimleri karşılamak için, birleştirme ve minification iş akışını Gulp kullanmak için dönüştürebilirsiniz.

### <a name="use-the-bundler--minifier-extension"></a>Bundler & Minifier uzantısını kullanma

Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) uzantısı Gulp'a dönüştürme işlemlerini işler.

> [!NOTE]
> Bundler & Minifier uzantısı, Microsoft'un destek sağlamadığı GitHub'daki topluluk odaklı bir projeye aittir. Sorunlar [burada](https://github.com/madskristensen/BundlerMinifier/issues)dosyalanmalıdır.

Solution Explorer'daki *bundleconfig.json* dosyasına sağ tıklayın ve **Bundler & Minifier** > **Convert To Gulp'u seçin...**:

![Gülle bağlam ı öğesini dönüştür](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

*gulpfile.js* ve *package.json* dosyaları projeye eklenir. *package.json* dosyasının `devDependencies` bölümünde listelenen destekleyici [nPM](https://www.npmjs.com/) paketleri yüklenir.

Genel bağımlılık olarak Gulp CLI yüklemek için PMC penceresinde aşağıdaki komutu çalıştırın:

```console
npm i -g gulp-cli
```

*gulpfile.js* dosyası, girişler, çıktılar ve ayarlar için *bundleconfig.json* dosyasını okur.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>El ile dönüştürme

Visual Studio ve/veya Bundler & Minifier uzantısı kullanılamıyorsa, el ile dönüştürün.

Proje köküne aşağıdaki `devDependencies`lerle birlikte bir *package.json* dosyası ekleyin:

> [!WARNING]
> Modül `gulp-uglify` ECMAScript (ES) 2015 / ES6 ve sonraki lerini desteklemez. ES2015 / `gulp-uglify` ES6 veya daha sonra kullanmak yerine [gulp-terser'i](https://www.npmjs.com/package/gulp-terser) yükleyin.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Aşağıdaki komutu *package.json*ile aynı düzeyde çalıştırarak bağımlılıkları yükleyin:

```console
npm i
```

Genel bağımlılık olarak Gulp CLI yükleyin:

```console
npm i -g gulp-cli
```

Aşağıdaki *gulpfile.js* dosyasını proje köküne kopyalayın:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp görevlerini çalıştır

Visual Studio'da proje oluşturmadan önce Gulp minification görevini tetiklemek için *.csproj dosyasına aşağıdaki [MSBuild Hedefini](/visualstudio/msbuild/msbuild-targets) ekleyin:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Bu örnekte, önceden tanımlanmış `MyPreCompileTarget` `Build` hedeften önce hedef çalıştırılan hedef içinde tanımlanan tüm görevler. Aşağıdakilere benzer çıktı Visual Studio'nun Çıkış penceresinde görünür:

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
