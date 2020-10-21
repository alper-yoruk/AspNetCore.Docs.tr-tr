---
title: ASP.NET MVC 'den ASP.NET Core MVC 'ye geçirmeyi öğrenin
author: wadepickett
description: ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye nasıl başlayacağınızı öğrenin.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326635"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş

::: moniker range=">= aspnetcore-3.0"

Bu makalede, ASP.NET MVC projesini [ASP.NET Core MVC](xref:mvc/overview)'ye geçirmeye nasıl başlayayapılacağı gösterilmektedir. Sürecinde, ASP.NET MVC 'den ilgili değişiklikleri vurgular.

ASP.NET MVC 'den geçiş çok adımlı bir işlemdir. Bu makalede şunları ele alınmaktadır:

* İlk kurulum.
* Temel denetleyiciler ve görünümler.
* Statik içerik.
* İstemci tarafı bağımlılıkları.

Yapılandırma ve kodu geçirmek için Identity bkz. [yapılandırmayı ASP.NET Core](xref:migration/configuration) ve [geçiş kimlik doğrulaması ve Identity ASP.NET Core geçirme](xref:migration/identity).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Başlatıcı ASP.NET MVC projesi oluşturma

Geçirmek için Visual Studio 'da örnek bir ASP.NET MVC projesi oluşturun:

1. **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
1. **ASP.NET Web uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.
1. Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesi ile eşleşen proje *WebApp1* olarak adlandırın. **Oluştur**’u seçin.
1. **MVC**' yi ve ardından **Oluştur**' u seçin.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Geçirilecek yeni bir ASP.NET Core projesi ile yeni bir çözüm oluşturun:

1. Visual Studio 'nun ikinci bir örneğini başlatın.
1. **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
1. **ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** iletişim kutusunda projeyi *WebApp1*olarak adlandırın.
1. Aynı proje adını kullanmak için konumu önceki projeden farklı bir dizine ayarlayın. Aynı ad alanının kullanılması, kodu iki proje arasında kopyalamayı kolaylaştırır. **Oluştur**’u seçin.
1. **Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın. **Web uygulaması (Model-View-Controller)** proje şablonunu seçin ve **Oluştur**' u seçin.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>ASP.NET Core sitesini MVC kullanacak şekilde yapılandırma

ASP.NET Core 3,0 ve sonraki projelerde .NET Framework artık desteklenen bir hedef çerçeve değildir. Projenizin .NET Core 'ı hedeflemesi gerekir. MVC 'yi içeren ASP.NET Core paylaşılan Framework, .NET Core çalışma zamanı yüklemesinin bir parçasıdır. Proje dosyasında SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur `Microsoft.NET.Sdk.Web` :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Daha fazla bilgi için bkz. [Framework başvurusu](xref:migration/22-to-30#framework-reference).

ASP.NET Core, `Startup` Sınıfı:

* *Global. asax*öğesini değiştirir.
* Tüm uygulama başlangıç görevlerini işler.

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

ASP.NET Core projesinde, *Startup.cs* dosyasını açın:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core uygulamalar, ara yazılım ile Framework özelliklerini kabul etmelidir. Şablon tarafından oluşturulan önceki kod, aşağıdaki hizmetleri ve ara yazılımı ekler:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Genişletme yöntemi denetleyiciler, API ile ilgili özellikler ve görünümler IÇIN MVC hizmeti desteğini kaydeder. MVC hizmeti kayıt seçenekleri hakkında daha fazla bilgi için bkz. [MVC hizmeti kaydı](xref:migration/22-to-30#mvc-service-registration)
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Genişletme yöntemi statik dosya işleyicisini ekler `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`Uzantı yönteminin önce çağrılması gerekir `UseRouting` . Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Uzantı yöntemi yönlendirme ekler. Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

Bu mevcut yapılandırma, örnek ASP.NET MVC projesini geçirmek için gerekenleri içerir. ASP.NET Core ara yazılım seçenekleri hakkında daha fazla bilgi için bkz <xref:fundamentals/startup> ..

## <a name="migrate-controllers-and-views"></a>Denetleyicileri ve görünümleri geçirme

ASP.NET Core projesinde, geçiş için herhangi bir ASP.NET MVC projesindeki denetleyici ve görünüm sınıflarıyla aynı adları kullanarak yer tutucu olarak görev yapmak üzere yeni bir boş denetleyici sınıfı ve görünüm sınıfı eklenir.

ASP.NET Core *WebApp1* projesi zaten en az bir örnek denetleyici içeriyor ve ASP.NET MVC projesiyle aynı ada sahip bir görünüm içeriyor. Bu nedenle, ASP.NET MVC denetleyicisi ve görünümleri ASP.NET MVC *WebApp1* projesinden geçirilecek görünümler için yer tutucu olarak görev yapar.

1. `HomeController`Yeni ASP.NET Core yöntemlerini değiştirmek için ASP.NET MVC içindeki yöntemleri kopyalayın `HomeController` . Eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez. ASP.NET MVC yerleşik şablonunun denetleyici eylem yöntemi dönüş türü <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC içinde eylem yöntemleri döndürülür `IActionResult` . `ActionResult` uygular `IActionResult` .
1. ASP.NET Core projesinde, *Görünümler/giriş* dizinine sağ tıklayın, **Add** > **Varolan öğe**Ekle ' yi seçin.
1. **Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler/giriş* dizinine gidin.
1. *Hakkında. cshtml*, *Contact. cshtml*ve *Index. cshtml* Razor Görünüm dosyalarını seçin ve ardından **Ekle**' yi seçerek var olan dosyaları değiştirin.

Daha fazla bilgi için <xref:mvc/controllers/actions> ve <xref:mvc/views/overview> bölümlerine bakın.

## <a name="test-each-method"></a>Her yöntemi test et

Her denetleyici uç noktası test edilebilir, ancak düzen ve stiller belgede daha sonra ele alınmıştır.

1. ASP.NET Core uygulamasını çalıştırın.
1. Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenen görünümleri çağırın. Örneğin, `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Statik içeriği geçirme

ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kök dizininden barındırılıyor ve sunucu tarafı dosyalarıyla karıştı. ASP.NET Core, statik dosyalar projenin [Web kök](xref:fundamentals/index#web-root) dizininde saklanır. Varsayılan dizin *{Content root}/Wwwroot*, ancak değiştirilebilir. Daha fazla bilgi için [ASP.NET Core Içindeki statik dosyalar](xref:fundamentals/static-files#serve-static-files)bölümüne bakın.

ASP.NET MVC *WebApp1* projesinden statik Içeriği ASP.NET Core *WebApp1* projesindeki *Wwwroot* dizinine kopyalayın:

1. ASP.NET Core projesinde, *Wwwroot* dizinine sağ tıklayın, **Add** > **Varolan öğe**Ekle ' yi seçin.
1. **Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesine gidin.
1. *Tercih simgesi. ico* dosyasını seçin ve ardından **Ekle**' yi seçerek var olan dosyayı değiştirin.

## <a name="migrate-the-layout-files"></a>Düzen dosyalarını geçirme

ASP.NET MVC proje düzeni dosyalarını ASP.NET Core projesine kopyalayın:

1. ASP.NET Core projesinde, *Görünümler* dizinine sağ tıklayın, **Add** > **Varolan öğe**Ekle ' yi seçin.
1. **Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler* dizinine gidin.
1. *_ViewStart. cshtml* dosyasını seçin ve ardından **Ekle**' yi seçin.

ASP.NET MVC proje paylaşılan düzen dosyalarını ASP.NET Core projesine kopyalayın:

1. ASP.NET Core projesinde, *Görünümler/paylaşılan* dizine sağ tıklayın, **Add** > **Varolan öğe**Ekle ' yi seçin.
1. **Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler/paylaşılan* dizinine gidin.
1. *_Layout. cshtml* dosyasını seçin ve ardından **Ekle**' yi seçerek var olan dosyayı değiştirin.

ASP.NET Core projesinde, *_Layout. cshtml* dosyasını açın. Aşağıda gösterilen tamamlanan kodla eşleştirmek için aşağıdaki değişiklikleri yapın:

Önyükleme CSS 'sini aşağıdaki tamamlanan kodla eşleşecek şekilde güncelleştirin:

1. `@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).
1. Kaldırın `@Scripts.Render("~/bundles/modernizr")` .

Önyükleme CSS ekleme için tamamlanan değiştirme biçimlendirmesi:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery ve Bootstrap JavaScript 'ı aşağıdaki tamamlanan kodla eşleşecek şekilde güncelleştirin:

1. `@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).
1. `@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).

JQuery ve Bootstrap JavaScript ekleme için tamamlanan değiştirme biçimlendirmesi:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Siteyi tarayıcıda görüntüleyin. Beklenen stillerle birlikte işlenmelidir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

ASP.NET Core, [Weboptimizer](https://github.com/ligershark/WebOptimizer) ve diğer benzer kitaplıklar gibi çeşitli açık kaynaklı paket oluşturma ve küçültmeye yönelik çözümlerle uyumludur. ASP.NET Core, yerel bir paketleme ve küçültmeye yönelik çözüm sağlamaz. Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.

## <a name="solve-http-500-errors"></a>HTTP 500 hatalarını çözme

Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır. Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur. ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve ortam *geliştirme*sırasında yürütülür. Bu, aşağıdaki kodda ayrıntılı olarak verilmiştir:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür. Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir. Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Sonraki adımlar

* <xref:migration/identity>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bu makalede, bir ASP.NET MVC projesinin [ASP.NET Core MVC](xref:mvc/overview) 2,2 ' ye nasıl geçirilerek yapılacağı gösterilmektedir. Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular. ASP.NET MVC 'den geçiş çok adımlı bir işlemdir. Bu makalede şunları ele alınmaktadır:

* İlk kurulum
* Temel denetleyiciler ve görünümler
* Statik içerik
* İstemci tarafı bağımlılıkları.

Yapılandırma ve kodu geçirmek için Identity bkz <xref:migration/configuration> . ve <xref:migration/identity> .

> [!NOTE]
> Örneklerdeki sürüm numaraları güncel olmayabilir, projeleri uygun şekilde güncelleştirin.

## <a name="create-the-starter-aspnet-mvc-project"></a>Başlatıcı ASP.NET MVC projesi oluşturma

Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız. Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesiyle eşleşmesi için *WebApp1* adıyla oluşturun.

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5*arasında değiştirin. Visual Studio yeni çözüm adını (*Mvc5*) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun (*WebApp1*), böylece iki projedeki ad alanları eşleşir. Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır. Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturun.

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun. Projeyi *WebApp1*olarak adlandırın ve **bireysel kullanıcı hesaplarının**bir kimlik doğrulama seçeneğini belirleyin. Bu uygulamayı *Fullaspnetcore*olarak yeniden adlandırın. Bu projenin oluşturulması, dönüştürmeye zaman kazandırır. Nihai sonuç, şablon tarafından üretilen kodda görüntülenebilir, kod dönüştürme projesine kopyalanabilir veya şablon tarafından oluşturulan projeyle karşılaştırılabilir.

## <a name="configure-the-site-to-use-mvc"></a>Siteyi MVC kullanacak şekilde yapılandırma

* .NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur. Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir. .NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.

`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC çerçevesidir. `Microsoft.AspNetCore.StaticFiles` , statik dosya işleyicisidir. ASP.NET Core uygulamalar, statik dosyalar sunma gibi bir ara yazılım için açıkça kabul edebilir. Daha fazla bilgi için bkz. [statik dosyalar](xref:fundamentals/static-files).

* *Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Genişletme yöntemi statik dosya işleyicisini ekler. Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Denetleyici ekleme ve görüntüleme

Bu bölümde, bir sonraki bölümde geçirilen ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm eklenmiştir.

* Bir *Controllers* dizini ekleyin.

* *Controllers* dizinine *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* Bir *Görünümler* dizini ekleyin.

* Bir *Görünüm/giriş* dizini ekleyin.

* *Görünümler/giriş* dizinine *Index. cshtml* adlı bir ** Razor Görünüm** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

Proje yapısı aşağıda gösterilmiştir:

![WebApp1 dosyalarının ve dizinlerinin gösterildiği Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:

```html
<h1>Hello world!</h1>
```

Uygulamayı çalıştırın.

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview).

Aşağıdaki işlev örnek ASP.NET MVC projesinden ASP.NET Core projesine geçiş gerektirir:

* istemci tarafı içerik (CSS, yazı tipleri ve betikler)

* denetleyiciler

* görünümler

* modeller

* Paketleme

* filtreler

* Oturum açma/kapatma Identity (Bu, sonraki öğreticide yapılır.)

## <a name="controllers-and-views"></a>Denetleyiciler ve görünümler

* Yöntemlerin her birini ASP.NET MVC 'den `HomeController` New öğesine kopyalayın `HomeController` . ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yöntemi dönüş türü ' dir <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC 'de, eylem yöntemleri `IActionResult` bunun yerine döndürülür. `ActionResult` uyguladığı `IActionResult` için, eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez.

* ASP.NET MVC projesindeki *. cshtml*, *Contact. cshtml*ve *Index. cshtml* Razor Görünüm dosyalarını ASP.NET Core projesine kopyalayın.

## <a name="test-each-method"></a>Her yöntemi test et

Düzen dosyası ve stiller henüz geçirilmemiştir, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir. Ve görünümleri için Düzen dosyası tarafından oluşturulan `About` Bağlantılar `Contact` henüz kullanılamayacak.

Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenmiş görünümleri çağırın. Örneğin: `https://localhost:44375/home/about`.

![Kişi sayfası](mvc/_static/contact-page.png)

Stil ve menü öğelerinin eksikliğine göz önünde. Stil, sonraki bölümde düzeltilecektir.

## <a name="static-content"></a>Statik içerik

ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kökünden barındırılır ve sunucu tarafı dosyalarıyla karıştı. ASP.NET Core, statik içerik *Wwwroot* dizininde barındırılır. ASP.NET MVC uygulamasındaki statik içeriği ASP.NET Core projesindeki *Wwwroot* dizinine kopyalayın. Bu örnek dönüştürmede:

* ASP.NET MVC projesinden ASP.NET Core projesindeki *Wwwroot* dizinine MVC *. ico* dosyasını kopyalayın.

ASP.NET MVC projesi, kendi stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* dizinlerinde depolar. ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur (*Görünümler/paylaşılan/_Layout. cshtml*). *bootstrap.js* ve *Bootstrap. css* dosyaları, ASP.NET MVC projesinden yeni projedeki *Wwwroot* dizinine kopyalanabilir. Bunun yerine, bu belge sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekler.

## <a name="migrate-the-layout-file"></a>Düzen dosyasını geçirme

* *_ViewStart. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler* dizininden ASP.NET Core projenin *Görünümler* dizinine kopyalayın. *_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.

* Bir *Görünüm/paylaşılan* dizin oluşturun.

* *Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* dizininden ASP.NET Core projenin *views* dizinine kopyalayın. *_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın. *_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir. Etiket Yardımcıları yeni düzen dosyasında kullanılır. *_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.

* *_Layout. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler/paylaşılan* dizininden ASP.NET Core projenin *Görünümler/paylaşılan* dizinine kopyalayın.

*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):

* `@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).

* Kaldırın `@Scripts.Render("~/bundles/modernizr")` .

* `@Html.Partial("_LoginPartial")`Çizgiyi açıklama (çizgi ile çevreleyin `@*...*@` ). Daha fazla bilgi için bkz. [kimlik doğrulamasını geçirme ve Identity ASP.NET Core](xref:migration/identity)

* `@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).

* `@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Siteyi tarayıcıda görüntüleyin. Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.

* *Isteğe bağlı:* Yeni düzen dosyasını kullanmayı deneyin. Düzen dosyasını *Fullaspnetcore* projesinden kopyalayın. Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.

## <a name="solve-http-500-errors"></a>HTTP 500 hatalarını çözme

Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır. Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur. ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme*sırasında yürütülür. Aşağıdaki kodda bir örnek görebilirsiniz:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür. Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir. Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Bu makalede, bir ASP.NET MVC projesinin [ASP.NET Core MVC](xref:mvc/overview) 2,1 ' ye nasıl geçirilerek yapılacağı gösterilmektedir. Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular. ASP.NET MVC 'den geçiş çok adımlı bir işlemdir. Bu makalede şunları ele alınmaktadır:

* İlk kurulum
* Temel denetleyiciler ve görünümler
* Statik içerik
* İstemci tarafı bağımlılıkları.

Yapılandırma ve kodu geçirmek için Identity bkz. [yapılandırmayı ASP.NET Core](xref:migration/configuration) ve [geçiş kimlik doğrulaması ve Identity ASP.NET Core geçirme](xref:migration/identity).

> [!NOTE]
> Örneklerdeki sürüm numaraları güncel olmayabilir, projeleri uygun şekilde güncelleştirin.

## <a name="create-the-starter-aspnet-mvc-project"></a>Başlatıcı ASP.NET MVC projesi oluşturma

Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız. Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesiyle eşleşmesi için *WebApp1* adıyla oluşturun.

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5*arasında değiştirin. Visual Studio yeni çözüm adını (*Mvc5*) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun (*WebApp1*), böylece iki projedeki ad alanları eşleşir. Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır. Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturun.

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun. Projeyi *WebApp1*olarak adlandırın ve **bireysel kullanıcı hesaplarının**bir kimlik doğrulama seçeneğini belirleyin. Bu uygulamayı *Fullaspnetcore*olarak yeniden adlandırın. Bu projenin oluşturulması, dönüştürmeye zaman kazandırır. Nihai sonuç, şablon tarafından üretilen kodda görüntülenebilir, kod dönüştürme projesine kopyalanabilir veya şablon tarafından oluşturulan projeyle karşılaştırılabilir.

## <a name="configure-the-site-to-use-mvc"></a>Siteyi MVC kullanacak şekilde yapılandırma

* .NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur. Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir. .NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.

`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC çerçevesidir. `Microsoft.AspNetCore.StaticFiles` , statik dosya işleyicisidir. ASP.NET Core uygulamalar, statik dosyalar sunma gibi bir ara yazılım için açıkça kabul edebilir. Daha fazla bilgi için bkz. [statik dosyalar](xref:fundamentals/static-files).

* *Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Genişletme yöntemi statik dosya işleyicisini ekler. `UseMvc`Uzantı yöntemi yönlendirme ekler. Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Denetleyici ekleme ve görüntüleme

Bu bölümde, bir sonraki bölümde geçirilen ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm eklenmiştir.

* Bir *Controllers* dizini ekleyin.

* *Controllers* dizinine *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* Bir *Görünümler* dizini ekleyin.

* Bir *Görünüm/giriş* dizini ekleyin.

* *Görünümler/giriş* dizinine *Index. cshtml* adlı bir ** Razor Görünüm** ekleyin.

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

Proje yapısı aşağıda gösterilmiştir:

![WebApp1 dosyalarının ve dizinlerinin gösterildiği Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:

```html
<h1>Hello world!</h1>
```

Uygulamayı çalıştırın.

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview).

Aşağıdaki işlev örnek ASP.NET MVC projesinden ASP.NET Core projesine geçiş gerektirir:

* istemci tarafı içerik (CSS, yazı tipleri ve betikler)

* denetleyiciler

* görünümler

* modeller

* Paketleme

* filtreler

* Oturum açma/kapatma Identity (Bu, sonraki öğreticide yapılır.)

## <a name="controllers-and-views"></a>Denetleyiciler ve görünümler

* Yöntemlerin her birini ASP.NET MVC 'den `HomeController` New öğesine kopyalayın `HomeController` . ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yöntemi dönüş türü ' dir <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC 'de, eylem yöntemleri `IActionResult` bunun yerine döndürülür. `ActionResult` uyguladığı `IActionResult` için, eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez.

* ASP.NET MVC projesindeki *. cshtml*, *Contact. cshtml*ve *Index. cshtml* Razor Görünüm dosyalarını ASP.NET Core projesine kopyalayın.

## <a name="test-each-method"></a>Her yöntemi test et

Düzen dosyası ve stiller henüz geçirilmemiştir, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir. Ve görünümleri için Düzen dosyası tarafından oluşturulan `About` Bağlantılar `Contact` henüz kullanılamayacak.

* Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenmiş görünümleri çağırın. Örneğin: `https://localhost:44375/home/about`.

![Kişi sayfası](mvc/_static/contact-page.png)

Stil ve menü öğelerinin eksikliğine göz önünde. Stil, sonraki bölümde düzeltilecektir.

## <a name="static-content"></a>Statik içerik

ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kökünden barındırılır ve sunucu tarafı dosyalarıyla karıştı. ASP.NET Core, statik içerik *Wwwroot* dizininde barındırılır. ASP.NET MVC uygulamasındaki statik içeriği ASP.NET Core projesindeki *Wwwroot* dizinine kopyalayın. Bu örnek dönüştürmede:

* ASP.NET MVC projesinden ASP.NET Core projesindeki *Wwwroot* dizinine MVC *. ico* dosyasını kopyalayın.

ASP.NET MVC projesi, kendi stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* dizinlerinde depolar. ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur (*Görünümler/paylaşılan/_Layout. cshtml*). *bootstrap.js* ve *Bootstrap. css* dosyaları, ASP.NET MVC projesinden yeni projedeki *Wwwroot* dizinine kopyalanabilir. Bunun yerine, bu belge sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekler.

## <a name="migrate-the-layout-file"></a>Düzen dosyasını geçirme

* *_ViewStart. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler* dizininden ASP.NET Core projenin *Görünümler* dizinine kopyalayın. *_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.

* Bir *Görünüm/paylaşılan* dizin oluşturun.

* *Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* dizininden ASP.NET Core projenin *views* dizinine kopyalayın. *_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın. *_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir. Etiket Yardımcıları yeni düzen dosyasında kullanılır. *_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.

* *_Layout. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler/paylaşılan* dizininden ASP.NET Core projenin *Görünümler/paylaşılan* dizinine kopyalayın.

*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):

* `@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).

* Kaldırın `@Scripts.Render("~/bundles/modernizr")` .

* `@Html.Partial("_LoginPartial")`Çizgiyi açıklama (çizgi ile çevreleyin `@*...*@` ). Daha fazla bilgi için bkz. [kimlik doğrulamasını geçirme ve Identity ASP.NET Core](xref:migration/identity)

* `@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).

* `@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Siteyi tarayıcıda görüntüleyin. Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.

* *Isteğe bağlı:* Yeni düzen dosyasını kullanmayı deneyin. Düzen dosyasını *Fullaspnetcore* projesinden kopyalayın. Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.

## <a name="solve-http-500-errors"></a>HTTP 500 hatalarını çözme

Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır. Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur. ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme*sırasında yürütülür. Aşağıdaki kodda bir örnek görebilirsiniz:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür. Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir. Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
