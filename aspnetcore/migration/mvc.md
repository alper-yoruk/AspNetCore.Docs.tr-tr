---
title: ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş
author: ardalis
description: ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye nasıl başlaleyeceğinizi öğrenin.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777052"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)ve [Scott Ade](https://scottaddie.com)

Bu makalede, bir ASP.NET MVC projesini [ASP.NET Core MVC](../mvc/overview.md)'ye geçirmeye nasıl başlacağınız gösterilmektedir. Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular. ASP.NET MVC 'den geçiş, birden çok adımlı bir işlemdir ve bu makalede ilk kurulum, temel denetleyiciler ve görünümler, statik içerik ve istemci tarafı bağımlılıkları ele alınmaktadır. Ek makaleler, birçok ASP.NET MVC projesinde bulunan yapılandırma ve kimlik kodunun geçirilmesini kapsar.

> [!NOTE]
> Örneklerdeki sürüm numaraları güncel olmayabilir. Projelerinizi uygun şekilde güncelleştirmeniz gerekebilir.

## <a name="create-the-starter-aspnet-mvc-project"></a>Başlatıcı ASP.NET MVC projesi oluşturma

Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız. Ad alanı, bir sonraki adımda oluşturduğumuz ASP.NET Core projeyle eşleşmesi için *WebApp1* adıyla oluşturun.

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5*arasında değiştirin. Visual Studio yeni çözüm adını (*Mvc5*) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun (*WebApp1*), böylece iki projedeki ad alanları eşleşir. Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır. Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturmanız gerekir.

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun. Projeyi *WebApp1*olarak adlandırın ve **bireysel kullanıcı hesaplarının**bir kimlik doğrulama seçeneğini belirleyin. Bu uygulamayı *Fullaspnetcore*olarak yeniden adlandırın. Bu projeyi oluşturmak, dönüştürmeye zaman kazandırır. Son sonucu görmek veya kodu dönüştürme projesine kopyalamak için, şablon tarafından oluşturulan koda bakabilirsiniz. Ayrıca, şablon tarafından oluşturulan projeyle karşılaştırmak için bir dönüştürme adımında takılı olduğunuzda da yararlıdır.

## <a name="configure-the-site-to-use-mvc"></a>Siteyi MVC kullanacak şekilde yapılandırma

::: moniker range=">= aspnetcore-2.1"

* .NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur. Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir. .NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* .NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. All metapackage](xref:fundamentals/metapackage) öğesine başvurulur. Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir. .NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* .NET Core veya .NET Framework hedeflenirken, MVC uygulamalarına göre yaygın olarak kullanılan paketler, proje dosyasında tek tek listelenir.

::: moniker-end

`Microsoft.AspNetCore.Mvc`ASP.NET Core MVC çerçevesidir. `Microsoft.AspNetCore.StaticFiles`, statik dosya işleyicisidir. ASP.NET Core çalışma zamanı Modüler olur ve statik dosyalara (bkz. [statik dosyaları](xref:fundamentals/static-files)) hizmeti sağlamak için açıkça oturum açmalısınız.

* *Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles` Genişletme yöntemi statik dosya işleyicisini ekler. Daha önce belirtildiği gibi, ASP.NET çalışma zamanı Modüler olur ve statik dosyaları sağlamak için açıkça kabul etmeniz gerekir. `UseMvc` Uzantı yöntemi yönlendirme ekler. Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Denetleyici ekleme ve görüntüleme

Bu bölümde, sonraki bölümde geçirebileceğiniz ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm ekleyeceksiniz.

* Bir *Controllers* klasörü ekleyin.

* *Controllers* klasörüne *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* Bir *Görünüm* klasörü ekleyin.

* Bir *Görünüm/giriş* klasörü ekleyin.

* *Görünümler/giriş* klasörüne *Index. cshtml* adlı bir ** Razor görünüm** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

Proje yapısı aşağıda gösterilmiştir:

![WebApp1 dosyalarını ve klasörlerini gösteren Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdakiler ile değiştirin:

```html
<h1>Hello world!</h1>
```

Uygulamayı çalıştırın.

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview) .

Artık en az çalışma ASP.NET Core projesi olduğuna göre, işlevselliği ASP.NET MVC projesinden geçirmeye başlayabiliriz. Aşağıdakileri taşıdık:

* istemci tarafı içerik (CSS, yazı tipleri ve betikler)

* denetleyiciler

* görünümler

* modeller

* Paketleme

* filtreler

* Oturum açma/kapatma Identity (Bu, sonraki öğreticide yapılır.)

## <a name="controllers-and-views"></a>Denetleyiciler ve görünümler

* Yöntemlerin her birini ASP.NET MVC `HomeController` 'den New `HomeController`öğesine kopyalayın. ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yönteminin dönüş türü [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET Core MVC 'de, eylem metotları bunun yerine `IActionResult` döndürülür. `ActionResult`uygular `IActionResult`, bu nedenle eylem yöntemlerinizi dönüş türünü değiştirmenize gerek yoktur.

* ASP.NET MVC projesindeki *. cshtml*, *Contact. cshtml*ve *Index. cshtml* Razor görünüm dosyalarını ASP.NET Core projesine kopyalayın.

* ASP.NET Core uygulamasını çalıştırın ve her yöntemi test edin. Düzen dosyasını veya stilleri henüz geçirmedik, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir. Ve görünümleri için Düzen dosyası oluşturulmuş bağlantılar yoktur, bu nedenle bunları tarayıcıdan çağırmanız gerekir (4492 değerini projenizde kullanılan bağlantı noktası numarasıyla değiştirin). **4492** `About` `Contact`

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Kişi sayfası](mvc/_static/contact-page.png)

Stil ve menü öğelerinin eksikliğine göz önünde. Sonraki bölümde bunu çözeceğiz.

## <a name="static-content"></a>Statik içerik

ASP.NET MVC 'nin önceki sürümlerinde, statik içerik Web projesinin kökünden barındırılıyor ve sunucu tarafı dosyalarıyla karıştı. ASP.NET Core, statik içerik *Wwwroot* klasöründe barındırılır. Eski ASP.NET MVC uygulamanızdan statik içeriği ASP.NET Core projenizdeki *Wwwroot* klasörüne kopyalamak isteyeceksiniz. Bu örnek dönüştürmede:

* *Ayrıcalıklı Icon. ico* dosyasını eski MVC projesinden ASP.NET Core projesindeki *Wwwroot* klasörüne kopyalayın.

Eski ASP.NET MVC projesi, stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* klasörlerinde depolar. Eski ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur (*Görünümler/paylaşılan/_Layout. cshtml*). ASP.NET MVC projesindeki *Bootstrap. js* ve *Bootstrap. css* dosyalarını yeni projedeki *Wwwroot* klasörüne kopyalayabilirsiniz. Bunun yerine, sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekleyeceğiz.

## <a name="migrate-the-layout-file"></a>Düzen dosyasını geçirme

* *_ViewStart. cshtml* dosyasını eskı ASP.NET MVC projesinin *Görünümler* klasöründen ASP.NET Core projesinin *Görünümler* klasörüne kopyalayın. *_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.

* Bir *Görünüm/paylaşılan* klasör oluşturun.

* *Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* klasöründen ASP.NET Core projenin *views* klasörüne kopyalayın. *_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın. *_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir. Etiket Yardımcıları yeni düzen dosyasında kullanılır. *_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.

* *_Layout. cshtml* dosyasını eskı ASP.NET MVC projesinin *Görünümler/paylaşılan* klasöründen ASP.NET Core projenin *Görünümler/paylaşılan* klasörüne kopyalayın.

*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):

* *Bootstrap. css* ' nin yükleneceği bir `@Styles.Render("~/Content/css")` `<link>` öğeyle değiştirin (aşağıya bakın).

* Kaldırın `@Scripts.Render("~/bundles/modernizr")`.

* `@Html.Partial("_LoginPartial")` Çizgiyi açıklama (çizgi ile `@*...*@`çevreleyin). Daha fazla bilgi için bkz. [kimlik Identity doğrulamasını geçirme ve ASP.NET Core](xref:migration/identity)

* Bir `@Scripts.Render("~/bundles/jquery")` `<script>` öğesiyle değiştirin (aşağıya bakın).

* Bir `@Scripts.Render("~/bundles/bootstrap")` `<script>` öğesiyle değiştirin (aşağıya bakın).

Önyükleme CSS ekleme için değiştirme biçimlendirmesi:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery ve Bootstrap JavaScript ekleme için değiştirme biçimlendirmesi:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Siteyi tarayıcıda görüntüleyin. Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.

* *Isteğe bağlı:* Yeni düzen dosyasını kullanmayı denemek isteyebilirsiniz. Bu proje için, düzen dosyasını *Fullaspnetcore* projesinden kopyalayabilirsiniz. Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](../client-side/bundling-and-minification.md)yönelik.

## <a name="solve-http-500-errors"></a>HTTP 500 hatalarını çözme

Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır. Örneğin, *views/_ViewImports. cshtml* dosyası projenizde mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası alırsınız. ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme*sırasında yürütülür. Bu, aşağıdaki kodda ayrıntılı olarak verilmiştir:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core, bir Web uygulamasındaki işlenmemiş özel durumları HTTP 500 hata yanıtlarına dönüştürür. Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir. Daha fazla bilgi için bkz. [tanıtıcı hatalarında](../fundamentals/error-handling.md) **Geliştirici özel durum sayfasını kullanma** .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
