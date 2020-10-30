---
title: ASP.NET Core hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarında hataların nasıl işleneceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060475"
---
# <a name="handle-errors-in-aspnet-core"></a>ASP.NET Core hataları işleme

::: moniker range=">= aspnetcore-5.0"

[Kirk Larkabağı](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından

Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır. Bkz <xref:web-api/handle-errors> . Web API 'leri için.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Nasıl indirilir](xref:index#how-to-download-a-sample).) F12 tarayıcı geliştirici araçlarındaki ağ sekmesi, örnek uygulamayı test ederken faydalıdır.

## <a name="developer-exception-page"></a>Geliştirici özel durum sayfası

*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler. ASP.NET Core şablonları aşağıdaki kodu oluşturur:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

Yukarıdaki vurgulanmış kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.

Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde oluşturulan özel durumları yakalayabilmesi için, ara yazılım ardışık düzenine erken yerleştirir.

Yukarıdaki kod, uygulama geliştirme ortamında çalıştırıldığında geliştirici özel durum sayfasını * **yalnızca** _ öğesine izin vermez. Ayrıntılı özel durum bilgileri, uygulama üretim ortamında çalıştırıldığında herkese açık bir şekilde gösterilmemelidir. Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..

Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:

_ Yığın izleme
* Varsa sorgu dizesi parametreleri
* Cookievarsa s
* Üst Bilgiler

## <a name="exception-handler-page"></a>Özel durum işleyici sayfası

[Üretim ortamı](xref:fundamentals/environments)için özel bir hata işleme sayfası yapılandırmak için çağrısı yapın <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Bu özel durum işleme ara yazılımı:

* Özel durumları yakalar ve günlüğe kaydeder.
* Belirtilen yolu kullanarak isteği alternatif bir ardışık düzende yeniden yürütür. Yanıt başlatılmışsa istek yeniden yürütülmez. Şablon tarafından oluşturulan kod, yolu kullanarak isteği yeniden yürütür `/Error` .

Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum işleme ara yazılımını geliştirme olmayan ortamlara ekler:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorSayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası ( *. cshtml* ) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sınıf ( `ErrorModel` ) sağlar *Pages* . MVC uygulaması için proje şablonu, `Error` giriş denetleyicisi için bir eylem yöntemi ve bir hata görünümü içerir.

Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` . Açık fiiller bazı isteklerin eylem yöntemine ulaşmasını önler. Kimliği doğrulanmamış kullanıcılar hata görünümünü görüyse, metoda anonim erişime izin verin.

### <a name="access-the-exception"></a>Özel duruma erişin

<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Bir hata işleyicisindeki özel duruma ve özgün istek yoluna erişmek için kullanın. Aşağıdaki kod, `ExceptionMessage` ASP.NET Core şablonları tarafından oluşturulan varsayılan *sayfalara/hata. cshtml. cs* öğesine ekler:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> İstemcilere **not** hassas hata bilgileri sunma. Hatalara hizmet vermek bir güvenlik riskidir.

[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)özel durumu sınamak için:

* Ortamı üretime ayarlayın.
* Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<Startup>();` . *Program.cs*
* Giriş sayfasında **özel durum tetikleme** ' yı seçin.

## <a name="exception-handler-lambda"></a>Özel durum işleyici lambda

Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.

Aşağıdaki kod, özel durum işleme için bir lambda kullanır:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> Ya **not** da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> . Hatalara hizmet vermek bir güvenlik riskidir.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)lambda işlemesini test etmek için:

* Ortamı üretime ayarlayın.
* Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupLambda>();` . *Program.cs*
* Giriş sayfasında **özel durum tetikleme** ' yı seçin.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Varsayılan olarak, bir ASP.NET Core uygulama HTTP hata durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz. Uygulama, gövdesi olmayan bir HTTP 400-499 hata durumuyla karşılaştığında, durum kodunu ve boş bir yanıt gövdesini döndürür. Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın. Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

`UseStatusCodePages`İstek işleme ara yazılımı için çağrı yapın. Örneğin, `UseStatusCodePages` statik dosya ara yazılımı ve uç nokta ara yazılımı aracılığıyla çağrı yapın.

`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür. Örneğin, sayfasına gitme `Home/Privacy2` . `UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` Genellikle üretimde kullanılmaz çünkü kullanıcılara faydalı olmayan bir ileti döndürür.

`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için:

* Ortamı üretime ayarlayın.
* Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupUseStatusCodePages>();` . *Program.cs*
* Giriş sayfasındaki giriş sayfasında bulunan bağlantıları seçin.

### <a name="usestatuscodepages-with-format-string"></a>Biçim dizesiyle UseStatusCodePages

Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

Yukarıdaki kodda, `{0}` hata kodu için bir yer tutucudur.

`UseStatusCodePages` bir biçim dizesi, genellikle üretimde kullanılmıyor çünkü kullanıcılara faydalı olmayan bir ileti döndürüyor.

`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupFormat>();` *program.cs* içindeki açıklamaları kaldırın.

### <a name="usestatuscodepages-with-lambda"></a>Lambda ile UseStatusCodePages

Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` Lambda ile, kullanıcılar için faydalı olmayan bir ileti döndürdüğünden, genellikle üretimde kullanılmaz.

`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupStatusLambda>();` *program.cs* içindeki açıklamaları kaldırın.

### <a name="usestatuscodepageswithredirects"></a>Usestatuscodepageswithyönlendirmeler

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:

* İstemciye [302 tarafından bulunan](https://developer.mozilla.org/docs/Web/HTTP/Status/302) bir durum kodu gönderir.
* İstemciyi, URL şablonunda belirtilen hata işleme uç noktasına yönlendirir. Hata işleme uç noktası genellikle hata bilgilerini görüntüler ve HTTP 200 döndürür.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

URL şablonu, `{0}` Önceki kodda gösterildiği gibi durum kodu için bir yer tutucu içerebilir. URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` . Uygulamada bir uç nokta belirtirken, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun. Bir Razor Sayfalar örneği için bkz. [Sample App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)'Teki [Pages/mystatuscode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) .

Bu yöntem genellikle uygulama şu şekilde kullanılır:

* , Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir. Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.
* İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.

`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCredirect>();` *program.cs* içindeki açıklamaları kaldırın.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:

* İstemciye özgün durum kodunu döndürür.
* Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Uygulama içindeki bir uç nokta belirtilmişse, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun. `UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun. Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.

Bu yöntem genellikle uygulama şunları yaparken kullanılır:

* Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin. Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.
* Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.

URL ve sorgu dizesi şablonları, durum kodu için bir yer tutucu içerebilir `{0}` . URL şablonu ile başlamalıdır `/` .

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.

`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCreX>();` *program.cs* içindeki açıklamaları kaldırın.

## <a name="disable-status-code-pages"></a>Durum kodu sayfalarını devre dışı bırak

MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için [[Skipstatuscodepages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) özniteliğini kullanın.

Bir Razor sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Özel durum işleme kodu

Özel durum işleme sayfalarındaki kod de özel durumlar oluşturabilir. Üretim hatası sayfaları tamamen test edilmelidir ve kendi özel durumlarını oluşturmaktan kaçınmak için ek bir işlem gerçekleştirir.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Yanıt üst bilgileri

Yanıt üstbilgileri gönderildikten sonra:

* Uygulama, yanıtın durum kodunu değiştiremiyor.
* Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz. Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.

## <a name="server-exception-handling"></a>Sunucu özel durum işleme

Bir uygulamadaki özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir. Yanıt üst bilgileri gönderilmeden sunucu bir özel durumu yakalarsa, sunucu yanıt `500 - Internal Server Error` gövdesi olmadan bir yanıt gönderir. Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır. Uygulama tarafından işlenmeyen istekler sunucu tarafından işlenir. Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir. Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.

## <a name="startup-exception-handling"></a>Başlatma özel durum işleme

Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir. Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.

Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir. Bağlama başarısız olursa:

* Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.
* DotNet işlemi kilitleniyor.
* HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.

[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Veritabanı hata sayfası

Veritabanı Geliştirici sayfası özel durum filtresi, `AddDatabaseDeveloperPageExceptionFilter` Entity Framework Core geçişleri kullanılarak çözümlenebilirler veritabanıyla ilgili özel durumları yakalar. Bu özel durumlar oluştuğunda, sorunu çözmek için olası eylemlerin ayrıntıları ile bir HTML yanıtı üretilir. Bu sayfa yalnızca geliştirme ortamında etkinleştirilmiştir. Aşağıdaki kod, Razor bireysel kullanıcı hesapları belirtildiğinde ASP.NET Core Pages şablonları tarafından oluşturulmuştur:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Özel durum filtreleri

MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir. RazorSayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler. Bu filtreler, bir denetleyici eyleminin ya da başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler. Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.

Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için faydalıdır, ancak yerleşik [özel durum işleme ara yazılımı](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)olarak esnek değildir `UseExceptionHandler` . `UseExceptionHandler`HANGI MVC eyleminin seçilmesinden farklı olarak hata işleme yapmanız gerekmediği müddetçe kullanmanızı öneririz.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Model durumu hataları

Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından

Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır. Bkz <xref:web-api/handle-errors> . Web API 'leri için.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Nasıl indirilir](xref:index#how-to-download-a-sample).)

## <a name="developer-exception-page"></a>Geliştirici özel durum sayfası

*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler. ASP.NET Core şablonları aşağıdaki kodu oluşturur:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Yukarıdaki kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.

Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde özel durumların yakalanabilmesi için herhangi bir ara yazılımın önüne yerleştirir.

Yukarıdaki kod, geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken** sunar. Ayrıntılı özel durum bilgileri, uygulama üretimde çalıştırıldığında herkese açık bir şekilde gösterilmemelidir. Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..

Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:

* Yığın izleme
* Varsa sorgu dizesi parametreleri
* Cookievarsa s
* Üst Bilgiler

## <a name="exception-handler-page"></a>Özel durum işleyici sayfası

Üretim ortamı için özel bir hata işleme sayfası yapılandırmak için özel durum Işleme ara yazılımını kullanın. Ara yazılım:

* Özel durumları yakalar ve günlüğe kaydeder.
* İsteği, belirtilen sayfa veya denetleyici için alternatif bir ardışık düzende yeniden yürütür. Yanıt başlatılmışsa istek yeniden yürütülmez. Oluşturulan şablon kodu, isteği yeniden yürütür `/Error` .

Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum Işleme ara yazılımını geliştirme olmayan ortamlara ekler:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorSayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası ( *. cshtml* ) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sınıf ( `ErrorModel` ) sağlar *Pages* . MVC uygulaması için proje şablonu, ana denetleyicide bir hata eylemi yöntemi ve bir hata görünümü içerir.

Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` . Açık fiiller bazı isteklerin yönteme ulaşmasını önler. Kimliği doğrulanmamış kullanıcılar hata görünümünü görüyse, metoda anonim erişime izin verin.

### <a name="access-the-exception"></a>Özel duruma erişin

<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Hata işleyicisi denetleyicisi veya sayfasındaki özel duruma ve özgün istek yoluna erişmek için kullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> İstemcilere **not** hassas hata bilgileri sunma. Hatalara hizmet vermek bir güvenlik riskidir.

Önceki özel durum işleme sayfasını tetiklemek için ortamı üretimler olarak ayarlayın ve bir özel durum zorlayın.

## <a name="exception-handler-lambda"></a>Özel durum işleyici lambda

Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.

Özel durum işleme için lambda kullanmanın bir örneği aşağıda verilmiştir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

Yukarıdaki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısı BIR IE hata iletisi yerine hata iletisini görüntüleyecek şekilde eklenmiştir. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.

> [!WARNING]
> Ya **not** da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> . Hatalara hizmet vermek bir güvenlik riskidir.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda işlemenin özel durum işleme sonucunu görmek için, `ProdEnvironment` ve ön `ErrorHandlerLambda` işlemci yönergelerini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Varsayılan olarak, bir ASP.NET Core uygulama HTTP durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz. Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür. Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.

Ara yazılım [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir.

Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

`UseStatusCodePages`İstek işleme ara yazılımı (örneğin, statik dosya ara yazılımı ve MVC ara yazılımı) önce çağırın.

`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür. Örneğin, sayfasına gitme `Home/Privacy2` . `UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>Biçim dizesiyle UseStatusCodePages

Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>Lambda ile UseStatusCodePages

Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>Usestatuscodepageswithyönlendirmeler

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:

* İstemciye *302 tarafından bulunan* bir durum kodu gönderir.
* İstemciyi, URL şablonunda belirtilen konuma yönlendirir.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir. URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` . Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun. Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.

Bu yöntem genellikle uygulama şu şekilde kullanılır:

* , Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir. Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.
* İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:

* İstemciye özgün durum kodunu döndürür.
* Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun. `UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun. Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.

Bu yöntem genellikle uygulama şunları yaparken kullanılır:

* Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin. Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.
* Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.

URL ve sorgu dizesi şablonları durum kodu için bir yer tutucu ( `{0}` ) içerebilir. URL şablonu eğik çizgiyle ( `/` ) başlamalıdır. Yolda bir yer tutucu kullanırken, uç noktanın (sayfa veya denetleyicinin) yol kesimini işleyediğini doğrulayın. Örneğin, bir Razor sayfa, yönergeyle isteğe bağlı yol segmenti değerini kabul etmelidir `@page` :

```cshtml
@page "{code?}"
```

Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Durum kodu sayfalarını devre dışı bırak

MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için özniteliğini kullanın [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .

Bir Razor sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Özel durum işleme kodu

Özel durum işleme sayfalarındaki kod özel durumlar oluşturabilir. Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.

### <a name="response-headers"></a>Yanıt üst bilgileri

Yanıt üstbilgileri gönderildikten sonra:

* Uygulama, yanıtın durum kodunu değiştiremiyor.
* Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz. Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.

## <a name="server-exception-handling"></a>Sunucu özel durum işleme

Uygulamanızın özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir. Sunucu yanıt üstbilgileri gönderilmeden önce bir özel durum yakalarsa, sunucu yanıt gövdesi olmadan *500-Iç sunucu hatası* yanıtı gönderir. Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır. Uygulamanız tarafından işlenmeyen istekler sunucu tarafından işlenir. Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir. Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.

## <a name="startup-exception-handling"></a>Başlatma özel durum işleme

Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir. Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.

Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir. Bağlama başarısız olursa:

* Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.
* DotNet işlemi kilitleniyor.
* HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.

[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Veritabanı hata sayfası

Veritabanı hata sayfası ara yazılımı, Entity Framework geçişleri kullanılarak çözümleneyolabilecek veritabanıyla ilgili özel durumları yakalar. Bu özel durumlar gerçekleştiğinde, sorunu çözmeye yönelik olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur. Bu sayfa yalnızca geliştirme ortamında etkinleştirilmelidir. Aşağıdakileri kod ekleyerek sayfayı etkinleştirin `Startup.Configure` :

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>[Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet paketini gerektirir.

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Özel durum filtreleri

MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir. RazorSayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler. Bu filtreler, bir denetleyici eyleminin veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler. Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak özel durum Işleme ara yazılımı kadar esnek değildir. Ara yazılımı kullanmanızı öneririz. Yalnızca hangi MVC eyleminin seçilmesinden ayrı olarak hata işleme yapmanız gereken durumlarda filtreleri kullanın.

## <a name="model-state-errors"></a>Model durumu hataları

Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
