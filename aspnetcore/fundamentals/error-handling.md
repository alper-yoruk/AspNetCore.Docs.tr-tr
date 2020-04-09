---
title: ASP.NET Core'daki hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarındaki hataları nasıl işleyeceğinizi keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658818"
---
# <a name="handle-errors-in-aspnet-core"></a>ASP.NET Core'daki hataları işleme

Yazar: [Tom Dykstra](https://github.com/tdykstra/) ve [Steve Smith](https://ardalis.com/)

Bu makalede, ASP.NET Core web uygulamalarında işleme hataları için ortak yaklaşımlar kapsar. Web <xref:web-api/handle-errors> API'leri için bkz.

[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([Nasıl indirilir](xref:index#how-to-download-a-sample).) Makale, farklı senaryoları etkinleştirmek için`#if`örnek `#endif` `#define`uygulamada önişlemci yönergelerinin (, , ) nasıl ayarlanacaklarına ilişkin yönergeler içerir.

## <a name="developer-exception-page"></a>Geliştirici Özel Durum Sayfası

*Geliştirici Özel Durum Sayfası,* istek özel durumları hakkında ayrıntılı bilgiler görüntüler. Sayfa [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir, Hangi [Microsoft.AspNetCore.App metapackage.](xref:fundamentals/metapackage-app) Uygulama Geliştirme `Startup.Configure` [ortamında](xref:fundamentals/environments)çalışırken sayfayı etkinleştirmek için yönteme kod ekleyin:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Aramayı, özel <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> durumları yakalamak istediğiniz herhangi bir ara yazılımdan önce yerleştirin.

> [!WARNING]
> Geliştirici Özel Durum Sayfasını **yalnızca uygulama Geliştirme ortamında çalışırken**etkinleştirin. Uygulama üretimde çalışırken ayrıntılı özel durum bilgilerini genel olarak paylaşmak istemezsinuz. Ortamları yapılandırma hakkında daha <xref:fundamentals/environments>fazla bilgi için bkz.

Sayfa, özel durum ve istek le ilgili aşağıdaki bilgileri içerir:

* Yığın izi
* Sorgu dize parametreleri (varsa)
* Çerezler (varsa)
* Üst bilgiler

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)Geliştirici Özel Durum Sayfasını görmek `DevEnvironment` için önişlemci yönergesini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.

## <a name="exception-handler-page"></a>Özel durum işleyicisi sayfası

Üretim ortamı için özel hata işleme sayfasını yapılandırmak için Özel Durum İşleme Aracı'nı kullanın. Ara yazılım:

* Özel durumları yakalar ve günlüğe kaydeder.
* Belirtilen sayfa veya denetleyici için alternatif bir ardışık ardışık durumda isteği yeniden yürütür. Yanıt başlatılırsa istek yeniden yürütülmez.

Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> Geliştirme dışı ortamlarda Özel Durum İşleme Aracı'nı ekler:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Razor Pages uygulama şablonu, *Sayfalar* klasöründe bir Hata`ErrorModel`sayfası (*.cshtml*) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> sınıf ( ) sağlar. Bir MVC uygulaması için proje şablonu bir Hata eylem yöntemi ve hata görünümü içerir. Eylem yöntemi aşağıda veda eder:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Hata işleyicisi eylem yöntemini HTTP yöntemi öznitelikleriyle `HttpGet`işaretlemeyin. Açık fiiller bazı isteklerin yönteme ulaşmasını engeller. Kimliği doğrulanmamış kullanıcıların hata görünümünü alabilmeleri için yönteme anonim erişime izin verin.

### <a name="access-the-exception"></a>Özel durum erişin

Hata <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> işleyicidenetleyicisi veya sayfasındaki özel durum ve özgün istek yoluna erişmek için kullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Hassas hata bilgilerini istemcilere **sunmayın.** Hizmet hataları bir güvenlik riskidir.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)özel durum işleme sayfasını görmek `ProdEnvironment` `ErrorHandlerPage` için, ve önişlemci yönergelerini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.

## <a name="exception-handler-lambda"></a>İstisna işleyicisi lambda

Özel bir [özel durum işleyicisi sayfasına](#exception-handler-page) <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>alternatif olarak bir lambda sağlamaktır. Lambda kullanmak yanıtı döndürmeden önce hataya erişim sağlar.

Aşağıda, özel durum kullanımı için lambda kullanmanın bir örneği verilmiştir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

Önceki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısının IE hata iletisi yerine hata iletisini görüntülemesi için eklenir. Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.

> [!WARNING]
> Müşterilerden veya <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> istemcilerden <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> hassas hata bilgileri **sunmayın.** Hizmet hataları bir güvenlik riskidir.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda'yı kullanma özel durum sonucunu `ProdEnvironment` görmek `ErrorHandlerLambda` için, ve önişlemci yönergelerini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.

## <a name="usestatuscodepages"></a>StatusCodePages'i Kullanma

Varsayılan olarak, bir ASP.NET Core uygulaması *404 - Bulunamadı*gibi HTTP durum kodları için bir durum kodu sayfası sağlamaz. Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür. Durum kodu sayfalarını sağlamak için Durum Kodu Sayfaları ara yazılımlarını kullanın.

Middleware [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)olan tarafından kullanılabilir hale getirilir.

Yaygın hata durum kodları için varsayılan metin yalnızca <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> işleyicileri etkinleştirmek `Startup.Configure` için, yöntemde arayın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Ara `UseStatusCodePages` yazılım işleme isteği önce arayın (örneğin, Statik Dosya Middleware ve MVC Middleware).

Varsayılan işleyiciler tarafından görüntülenen metnin bir örneği aşağıda verilmiştir:

```
Status Code: 404; Not Found
```

[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)çeşitli durum kodu sayfa biçimlerinden birini görmek `StatusCodePages`için, başlangıç olan önişlemci yönergelerinden birini kullanın ve ana sayfada **404** Tetikle'yi seçin.

## <a name="usestatuscodepages-with-format-string"></a>Biçim dizesi ile StatusCodePages'i kullanma

Yanıt içeriği türünü ve metnini özelleştirmek için, içerik türü ve biçim dizesini <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> alan aşırı yükkullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>Lambda ile StatusCodePages'i Kullanma

Özel hata işleme ve yanıt yazma kodu belirtmek için, bir lambda ifadesi alır aşırı yük <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> kullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Uzatma <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> yöntemi:

* İstemciye *302 - Bulunan* durum kodunu gönderir.
* İstemciyi URL şablonunda sağlanan konuma yönlendirir.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir. URL şablonu bir tilde (~) ile başlarsa, tilde uygulamanın `PathBase`. Uygulama içinde bir bitiş noktası işaret ederseniz, bitiş noktası için bir MVC görünümü veya Razor sayfası oluşturun. Jilet Sayfaları örneği için [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode.cshtml* 'e bakın.

Bu yöntem genellikle uygulama aşağıdaki nde kullanılır:

* İstemciyi genellikle farklı bir uygulamanın hatayı işlediği durumlarda farklı bir bitiş noktasına yönlendirmelidir. Web uygulamaları için istemcinin tarayıcı adresi çubuğu yeniden yönlendirilen bitiş noktasını yansıtır.
* Özgün durum kodunu ilk yönlendirme yanıtıyla korumamalı ve döndürmemelidir.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithreexecute

Uzatma <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> yöntemi:

* Özgün durum kodunu istemciye verir.
* Alternatif bir yol kullanarak istek ardışık hattını yeniden çalıştırarak yanıt gövdesini oluşturur.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Uygulama içinde bir bitiş noktası işaret ederseniz, bitiş noktası için bir MVC görünümü veya Razor sayfası oluşturun. Jilet Sayfaları örneği için [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode.cshtml* 'e bakın.

Bu yöntem genellikle uygulama nın aşağıdakileri yapması gerektiğinde kullanılır:

* İsteği farklı bir bitiş noktasına yönlendirmeden işle. Web uygulamaları için, istemcinin tarayıcı adresi çubuğu başlangıçta istenen bitiş noktasını yansıtır.
* Yanıtla birlikte özgün durum kodunu koruyun ve döndürün.

URL ve sorgu dize şablonları durum`{0}`kodu için bir yer tutucu ( ) içerebilir. URL şablonu bir eğik`/`çizgi ile başlamalıdır ( ). Yolda bir yer tutucu kullanırken, bitiş noktasının (sayfa veya denetleyicinin) yol kesimini işleyebileceğini onaylayın. Örneğin, hatalar için bir Razor Page `@page` yönergesi ile isteğe bağlı yol segment değeri kabul etmelidir:

```cshtml
@page "{code?}"
```

Hatayı işleyen bitiş noktası, aşağıdaki örnekte gösterildiği gibi hatayı oluşturan özgün URL'yi alabilir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Durum kodu sayfalarını devre dışı bırakma

MVC denetleyicisi veya eylem yöntemi için durum kodu [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) sayfalarını devre dışı kullanabilirsiniz, özniteliği kullanın.

Razor Pages işleyicisi yönteminde veya Bir MVC denetleyicisinde belirli istekler için durum kodu sayfalarını devre dışı kullanabilirsiniz: <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Özel durum işleme kodu

Özel durum işleme sayfalarındaki kod özel durumlar atabilir. Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.

### <a name="response-headers"></a>Yanıt üst bilgileri

Yanıt için üstbilgi gönderildikten sonra:

* Uygulama yanıtın durum kodunu değiştiremez.
* Herhangi bir özel durum sayfaları veya işleyicileri çalıştıramaz. Yanıt tamamlanmalı veya bağlantı iptal edilmelidir.

## <a name="server-exception-handling"></a>Sunucu özel durum işleme

Uygulamanızdaki özel durum işleme mantığına ek olarak, [HTTP sunucu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir. Yanıt üstbilgisi gönderilmeden önce sunucu bir özel durum yakalarsa, yanıt gövdesi olmadan sunucu *500 - Internal Server Error* yanıtı gönderir. Yanıt üstbilgisi gönderildikten sonra sunucu bir özel durum yakalarsa, sunucu bağlantıyı kapatır. Uygulamanız tarafından işlenmemiş istekler sunucu tarafından işlenir. Sunucu isteği işlerken oluşan herhangi bir özel durum, sunucunun özel durum işleme tarafından işlenir. Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımve filtreler bu davranışı etkilemez.

## <a name="startup-exception-handling"></a>Başlangıç özel durum işleme

Yalnızca barındırma katmanı, uygulama nın başlatılması sırasında gerçekleşen özel durumları işleyebilir. Ana [bilgisayar, başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.

Barındırma katmanı, yakalanan bir başlangıç hatası için yalnızca ana bilgisayar adresi/bağlantı noktası bağlama sonrasında hata oluşursa bir hata sayfası gösterebilir. Bağlama başarısız olursa:

* Barındırma katmanı kritik bir özel durum günlükleri.
* Dotnet işlemi çöküyor.
* HTTP sunucusu [Kerkenez](xref:fundamentals/servers/kestrel)olduğunda hiçbir hata sayfası görüntülenmez.

[IIS](/iis) (veya Azure Uygulama Hizmeti) veya [IIS Express'te](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, *502,5 - İşlem Hatası,* işlem başlatılamazsa [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) tarafından döndürülür. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Veritabanı hata sayfası

Veritabanı Hata Sayfası Middleware, Entity Framework geçişlerini kullanarak çözülebilen veritabanıyla ilgili özel durumları yakalar. Bu özel durumlar oluştuğunda, sorunu gidermek için olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur. Bu sayfa yalnızca Geliştirme ortamında etkinleştirilmelidir. `Startup.Configure`Aşağıdakilere kod ekleyerek sayfayı etkinleştirin:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Özel durum filtreleri

MVC uygulamalarında, özel durum filtreleri genel olarak veya denetleyici başına veya eylem başına olarak yapılandırılabilir. Razor Pages uygulamalarında, bunlar genel olarak veya sayfa modeli başına yapılandırılabilir. Bu filtreler, denetleyici eylemi veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumu işler. Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak Özel Durum İşleme Aracı kadar esnek değildir. Ara yazılımı kullanmanızı öneririz. Filtreleri yalnızca mvc eyleminin seçildiğine göre farklı hata işleme gerçekleştirmeniz gereken yerlerde kullanın.

## <a name="model-state-errors"></a>Model durum hataları

Model durumu hatalarının nasıl işleyeceğiniz hakkında bilgi için [Model bağlama](xref:mvc/models/model-binding) ve [Model doğrulamasına](xref:mvc/models/validation)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
