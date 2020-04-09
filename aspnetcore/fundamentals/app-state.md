---
title: ASP.NET Çekirdek oturum
author: rick-anderson
description: İstekler arasındaki oturumu korumak için yaklaşımları keşfedin.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 85d2a418c3aaae40bbcdc040095c2c98d4b7242c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80640039"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core'da oturum ve devlet yönetimi

::: moniker range=">= aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), ve Diana [LaRose](https://github.com/DianaLaRose)

HTTP vatansız bir protokoldür. Varsayılan olarak, HTTP istekleri kullanıcı değerlerini korumayan bağımsız iletilerdir. Bu makalede, istekler arasında kullanıcı verilerini korumak için çeşitli yaklaşımlar açıklanmaktadır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Durum yönetimi

Durum çeşitli yaklaşımlar kullanılarak depolanabilir. Her yaklaşım daha sonra bu konuda açıklanmıştır.

| Depolama yaklaşımı | Depolama mekanizması |
| ---------------- | ----------------- |
| [Çerezler](#cookies) | HTTP çerezleri. Sunucu tarafındaki uygulama kodu kullanılarak depolanan verileri içerebilir. |
| [Oturum durumu](#session-state) | HTTP çerezleri ve sunucu tarafı uygulama kodu |
| [Geçici Veriler](#tempdata) | HTTP çerezleri veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [httpContext.Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |

## <a name="cookies"></a>Tanımlama bilgileri

Çerezler verileri istekler arasında depolar. Çerezler her istekle gönderildiğinden, boyutları minimumda tutulmalıdır. İdeal olarak, uygulama tarafından depolanan verilerle birlikte bir çerezde yalnızca bir tanımlayıcı depolanmalıdır. Çoğu tarayıcı çerez boyutunu 4096 baytile kısıtlar. Her etki alanı için yalnızca sınırlı sayıda tanımlama bilgisi kullanılabilir.

Tanımlama bilgileri kurcalamaya tabi olduğundan, uygulama tarafından doğrulanmalıdır. Çerezler kullanıcılar tarafından silinebilir ve istemcilerin süresi dolabilir. Ancak, tanımlama bilgileri genellikle istemcideki en kalıcı veri biçimidir.

Çerezler genellikle içeriğin bilinen bir kullanıcı için özelleştirildiği kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlanır ve çoğu durumda kimliği doğrulanmaz. Çerez, kullanıcının adını, hesap adını veya GUID gibi benzersiz kullanıcı kimliğini depolayabilir. Çerez, kullanıcının tercih ettikleri web sitesi arka plan rengi gibi kişiselleştirilmiş ayarlarına erişmek için kullanılabilir.

Çerezleri ve gizlilik sorunlarıyla uğraşırken Avrupa Birliği Genel Veri Koruma Yönetmelikleri'ne [(GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) bakın. Daha fazla bilgi için [ASP.NET Core'daki Genel Veri Koruma Yönetmeliği (GDPR) desteğine](xref:security/gdpr)bakın.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, kullanıcı bir web uygulamasına göz atarken kullanıcı verilerinin depolanması için ASP.NET bir Çekirdek senaryosudur. Oturum durumu, bir istemciden gelen istekler arasında verileri sürdürmek için uygulama tarafından tutulan bir depokullanır. Oturum verileri bir önbellek tarafından desteklenen ve geçici veriler olarak kabul edilir. Site oturum verileri olmadan çalışmaya devam etmelidir. Kritik uygulama verileri kullanıcı veritabanında depolanmalı ve yalnızca performans optimizasyonu olarak oturumda önbelleğe alınmalıdır.

[SignalR Hub,](xref:signalr/hubs) HTTP bağlamından bağımsız olarak yürütebileceğinden, Oturum [SignalR](xref:signalr/index) uygulamalarında desteklenmez. Örneğin, bu, uzun bir yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda oluşabilir.

ASP.NET Core, istemciye oturum kimliği içeren bir tanımlama bilgisi sağlayarak oturum durumunu korur. Çerez oturum kimliği:

* Her istekle birlikte uygulamaya gönderilir.
* Oturum verilerini almak için uygulama tarafından kullanılır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum çerezi tarayıcıya özgüdür. Oturumlar tarayıcılar arasında paylaşılmaz.
* Oturum çerezleri tarayıcı oturumu sona erdiğinde silinir.
* Süresi dolmuş bir oturum için çerez alınırsa, aynı oturum çerezini kullanan yeni bir oturum oluşturulur.
* Boş oturumlar tutulmaz. Oturum, istekler arasında oturumu sürdürmek için en az bir değer kümesine sahip olmalıdır. Bir oturum korunmuyorsa, her yeni istek için yeni bir oturum kimliği oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre için bir oturum tutar. Uygulama oturum zaman anına ayarlar veya varsayılan değeri 20 dakika kullanır. Oturum durumu, kullanıcı verilerini depolamak için idealdir:
  * Bu belirli bir seansa özgü.
  * Verilerin oturumlar arasında kalıcı depolama gerektirmediği durumlarda.
* [Oturum verileri, ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) uygulaması çağrıldığında veya oturumun süresi dolduğunda silinir.
* Uygulama kodunu istemci tarayıcısının kapatıldığını veya oturum çerezinin istemci üzerinde silindiğini veya süresi nin dolduğunu bildiren varsayılan bir mekanizma yoktur.
* Oturum durum tanımlama bilgileri varsayılan olarak önemli olarak işaretlenmemiş. İzleme site ziyaretçisi tarafından izin verilmedikçe oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Hassas verileri oturum durumunda depolamayın. Kullanıcı tarayıcıyı kapatıp oturum çerezini temizlemeyebilir. Bazı tarayıcılar tarayıcı pencerelerinde geçerli oturum tanımlama bilgilerini korur. Oturum tek bir kullanıcıyla sınırlı olmayabilir. Bir sonraki kullanıcı aynı oturum çereziyle uygulamaya göz atmaya devam edebilir.

Bellek önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Bir sunucu çiftliği senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure Uygulama Hizmeti,](https://azure.microsoft.com/services/app-service/) yapışkan oturumları varsayılan olarak zorlamak için [Uygulama İsteği Yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve web uygulaması güncelleştirmelerini karmaşıklaştırabilir. Daha iyi bir yaklaşım yapışkan oturumları gerektirmeyen bir Redis veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum [çerezi IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)üzerinden şifrelenir. Veri Koruması, her makinede oturum tanımlama bilgilerini okumak için düzgün bir şekilde yapılandırılmalıdır. Daha fazla bilgi <xref:security/data-protection/introduction> için bkz ve [Anahtar depolama sağlayıcıları.](xref:security/data-protection/implementation/key-storage-providers)

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi:

* Çerçeve tarafından örtülü olarak yer alan.
* Oturum durumunu yönetmek için ara yazılım sağlar.

Oturum ara sını etkinleştirmek için aşağıdakileri `Startup` içermelidir:

* [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) bellek önbelleklerinden herhangi biri. Uygulama, `IDistributedCache` oturum için destek deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) için `ConfigureServices`bir çağrı .
* 'de `Configure` [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) için bir çağrı

Aşağıdaki kod, bellek içi oturum sağlayıcısının varsayılan bellek içi uygulamasıyla `IDistributedCache`nasıl ayarlanır:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,39)]

Önceki kod, sınama kolaylaştırmak için kısa bir zaman ayırıcı ayarlar.

Ara yazılım sırası önemlidir.  Sonra `UseSession` `UseRouting` ve `UseEndpoints`öncesinde arayın. Bkz. [Middleware Siparişi.](xref:fundamentals/middleware/index#order)

[HttpContext.Session,](xref:Microsoft.AspNetCore.Http.HttpContext.Session) oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce `UseSession` erişilemez.

Uygulama yanıt akışına yazmaya başladıktan sonra yeni oturum çerezi içeren yeni bir oturum oluşturulamaz. Özel durum web sunucusu günlüğüne kaydedilir ve tarayıcıda görüntülenmez.

### <a name="load-session-state-asynchronously"></a>Yük oturumu durumu eşzamanlı olarak

ASP.NET Core'daki varsayılan oturum sağlayıcısı, alttaki [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) destek deposundan yalnızca [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)veya [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) yöntemlerinden önce açıkça çağrıldığı takdirde oturum kayıtlarını yükler. [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) Önce `LoadAsync` çağrılmazsa, temel oturum kaydı eşzamanlı olarak yüklenir ve bu da ölçekte bir performans cezasına neden olabilir.

Uygulamaların bu deseni uygulamasına sahip olması için [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) ve [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) `LoadAsync` uygulamalarını, yöntem daha `TryGetValue` `Set`önce `Remove`çağrılmıyorsa özel durum oluşturan sürümlerle sarın, veya . Sarılmış sürümleri hizmet konteynerine kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için [SessionOptions'ı](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)kullanın.

| Seçenek | Açıklama |
| ------ | ----------- |
| [Çerez](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Tanımlama bilgisini oluşturmak için kullanılan ayarları belirler. [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`) için [ad](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) varsayılanları. [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) () için`/` [yol](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) varsayılandır. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) varsayılan [sameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) `1`( ). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan `true`. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) varsayılan `false`. |
| [ıdletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Oturum, `IdleTimeout` içeriği terk edilmeden önce ne kadar süre yle boşta kalarak durabileceğini gösterir. Her oturum erişimi zaman anına sıfırlanır. Bu ayar yalnızca çerez için değil, oturumun içeriği için geçerlidir. Varsayılan değer 20 dakikadır. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Bir oturumu niçin depodan yüklemeye veya mağazaya geri işlemek için izin verilen maksimum süre. Bu ayar yalnızca eşzamanlı işlemler için geçerli olabilir. Bu zaman aralığı [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan)kullanılarak devre dışı alınabilir. Varsayılan değer 1 dakikadır. |

Session, tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir çerez kullanır. Varsayılan olarak, bu `.AspNetCore.Session`çerez adlanır ve `/`bir yol kullanır. Çerez varsayılan bir etki alanı belirtmedığından, sayfadaki istemci tarafındaki komut dosyası için kullanılamaz (çünkü [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan `true`olarak).

Çerez oturumu varsayılanlarını geçersiz <xref:Microsoft.AspNetCore.Builder.SessionOptions>kılmak için şunları kullanın:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Uygulama, sunucunun önbelleğindeki içeriği terk edilmeden önce bir oturumun ne kadar süreyle boşta bırakılabildiğini belirlemek için [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) özelliğini kullanır. Bu özellik çerez son kullanma tarihinden bağımsızdır. [Session Middleware'den](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) geçen her istek zaman anına sıfırlar.

Oturum durumu *kilitlenmez.* İki istek aynı anda bir oturumun içeriğini değiştirmeye çalışırsa, son istek ilkini geçersiz kılar. `Session`tutarlı bir *oturum*olarak uygulanır, bu da tüm içeriğin birlikte depolandığı anlamına gelir. İki istek farklı oturum değerlerini değiştirmek istediğinde, son istek ilki tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve alma

Oturum durumuna, [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session)ile Bir Jilet Sayfaları [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) sınıfından veya MVC [Denetleyici](/dotnet/api/microsoft.aspnetcore.mvc.controller) sınıfından erişilir. Bu özellik bir [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) uygulamasıdır.

Uygulama, `ISession` tamsayı ve dize değerlerini ayarlamak ve almak için çeşitli uzantı yöntemleri sağlar. Uzantı yöntemleri [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) ad alanındadır.

`ISession`uzatma yöntemleri:

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Aşağıdaki örnek, Bir Razor Pages `IndexModel.SessionKeyName` sayfasında`_Name` anahtar (örnek uygulamada) için oturum değerini alır:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve dize nasıl ayarlanıp alınır:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek önbelleği kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek için seri hale getirilmelidir. String ve insalı seriizanlar [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)uzantısı yöntemleri ile sağlanır. Karmaşık türleri JSON gibi başka bir mekanizma kullanılarak kullanıcı tarafından seri hale getirilmelidir.

Nesneleri serihale getirmek için aşağıdaki örnek kodu kullanın:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, sınıfla birlikte serileştirilebilir bir nesnenin `SessionExtensions` nasıl ayarlanıp alınılsüreceğini gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>Geçici Veriler

ASP.NET Core, Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller'ı <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>ortaya çıkarır. Bu özellik, verileri başka bir istekte okunana kadar depolar. İstek sonunda silinmeden verileri incelemek için [Tut(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri kullanılabilir. Bekletme için sözlükteki tüm öğeleri işaretleri [tutun.](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) `TempData`şudur:

* Tek bir istekten fazlası için veri gerektiğinde yeniden yönlendirme için yararlıdır.
* Tanımlama bilgileri `TempData` veya oturum durumu kullanan sağlayıcılar tarafından uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa `TempData["Message"]`görüntülenir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki biçimlendirmede, isteğin sonunda, `TempData["Message"]` kullanılmadan dolayı `Peek` **silinmez.** Sayfanın yenilenmesi içeriğini `TempData["Message"]`görüntüler.

Aşağıdaki biçimlendirme önceki koda benzer, ancak `Keep` isteğin sonundaverileri korumak için kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*IndexPeek* ve *IndexKeep* sayfaları arasında gezinme `TempData["Message"]`siler.

Aşağıdaki kod `TempData["Message"]`görüntülenir, ancak isteğin sonunda, `TempData["Message"]` silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

Çerez tabanlı TempData sağlayıcısı, TempData'yı tanımlama bilgilerinde depolamak için varsayılan olarak kullanılır.

Çerez verileri [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kullanılarak şifrelenir, [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder)ile kodlanır , sonra öden. Maksimum çerez boyutu, şifreleme ve yığınlama nedeniyle [4096 bayttan](http://www.faqs.org/rfcs/rfc2965.html) daha azdır. Şifrelenmiş verileri sıkıştırmak [SUÇ](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [İhLAL](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açabileceğinden çerez verileri sıkıştırılmış değildir. Çerez tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için [CookieTempDataProvider'a](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider)bakın.

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

TempData sağlayıcısı seçmek, şu gibi çeşitli hususlar içerir:

* Uygulama zaten oturum durumunu kullanıyor mu? Bu durumda, oturum durumu TempData sağlayıcısını kullanarak uygulamaiçin verilerin boyutunun ötesinde ek bir maliyeti yoktur.
* Uygulama TempData'yı sadece 500 bayta kadar nispeten küçük miktarda veri için mi kullanıyor? Bu nedenle, çerez TempData sağlayıcısı TempData taşıyan her istek için küçük bir maliyet ekler. Değilse, oturum durumu TempData sağlayıcısı, TempData tüketilene kadar her istekte büyük miktarda veriyi yuvarlatmayı önlemek için yararlı olabilir.
* Uygulama birden çok sunucudaki bir sunucu çiftliğinde mi çalışır? Bu nedenle, Veri Koruması dışında çerez TempData sağlayıcısını kullanmak için <xref:security/data-protection/introduction> ek yapılandırma gerekmez (bkz. ve [Anahtar depolama sağlayıcıları).](xref:security/data-protection/implementation/key-storage-providers)

Web tarayıcıları gibi çoğu web istemcisi, her çerezin maksimum boyutu ve toplam tanımlama bilgisi sayısı yla ilgili sınırlar uygular. Çerez TempData sağlayıcısını kullanırken, uygulamanın [bu sınırları](http://www.faqs.org/rfcs/rfc2965.html)aşmayacağını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. Şifreleme ve yığınlama nedeniyle çerez boyutundaki artışları hesaba katın.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

Çerez tabanlı TempData sağlayıcısı varsayılan olarak etkinleştirilir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) uzantı yöntemini kullanın. Yalnızca bir `AddSessionStateTempDataProvider` arama gereklidir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Sorgu dizeleri

Sınırlı miktarda veri, yeni isteğin sorgu dizesine eklenerek bir istekten diğerine aktarılabilir. Bu, e-posta veya sosyal ağlar aracılığıyla katıştırılmış durumla bağlantının paylaşılmasına olanak tanıyan kalıcı bir şekilde durumu yakalamak için yararlıdır. URL sorgu dizeleri herkese açık olduğundan, hassas veriler için sorgu dizelerini asla kullanmayın.

Sorgu dizelerinde veri de dahil olmak üzere istenmeyen paylaşıma ek olarak, uygulamayı [Siteler Arası İstek Sahteciliği (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırılarına maruz bırakabilir. Herhangi bir korunmuş oturum durumu CSRF saldırılarına karşı korumalıdır. Daha fazla bilgi için [bkz.](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve bir sonraki isteğe nakledilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verileri kurcalayabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden geçersiz kılmalıdır.

## <a name="httpcontextitems"></a>httpContext.Items

[HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) koleksiyonu, tek bir isteği işlerken verileri depolamak için kullanılır. Bir istek işlendikten sonra koleksiyonun içeriği atılır. Koleksiyon `Items` genellikle bileşenlerin veya ara yazılımların istek sırasında farklı noktalarda çalıştıklarında iletişim kurmasına izin vermek için kullanılır ve parametreleri aktarmanın doğrudan bir yolu yoktur.

Aşağıdaki örnekte, [ara](xref:fundamentals/middleware/index) `isVerified` yazılım `Items` koleksiyona ekler:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Yalnızca tek bir uygulamada kullanılan ara yazılımlar `string` için sabit tuşlar kabul edilebilir. Uygulamalar arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne tuşlarını kullanmalıdır. Aşağıdaki örnek, ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, ara yazılım `HttpContext.Items` sınıfıtarafından açığa çıkarılan anahtarı kullanarak depolanan değere erişebilir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım, koddaki anahtar dizelerin kullanımını ortadan kaldırma avantajına da sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamanın ve almanın etkili bir yoludur. Uygulama önbelleğe alınmış öğelerin kullanım ömrünü denetleyebilir. Daha fazla bilgi için bkz. <xref:performance/caching/response>.

Önbelleğe alınan veriler belirli bir istek, kullanıcı veya oturumla ilişkili değildir. **Diğer kullanıcı istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe almayın.**

Uygulama geniş verilerini önbelleğe almak için bkz. <xref:performance/caching/memory>

## <a name="common-errors"></a>Sık karşılaşılan hatalar

* "Microsoft.AspNetCore.Session.DistributedSessionStore'u etkinleştirmeye çalışırken 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' türü için hizmet çözümlenemiyor."

  Bu genellikle en az bir `IDistributedCache` uygulama yapılandırmak için başarısız kaynaklanır. Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

Oturum ara yazılımı oturumu devam ettiremezse:

* Ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder.
* Bu öngörülemeyen davranışlara yol açar.

Destek deposu kullanılamıyorsa oturum ara yazılımı oturumu devam ettiremeyebilirsiniz. Örneğin, bir kullanıcı oturumda bir alışveriş sepeti depolar. Kullanıcı sepete bir öğe ekler, ancak işleme başarısız olur. Uygulama nın hatadan haberi olmadığı için kullanıcıya öğenin sepetine eklendiği bildirildiği ve bu doğru değildir.

Hataları denetlemek için önerilen yaklaşım, `await feature.Session.CommitAsync` uygulama oturuma yazma bittiğinde aramaktır. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>destek deposu kullanılamıyorsa bir özel durum oluşturur. Başarısız `CommitAsync` olursa, uygulama özel durumu işleyebilir. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>veri deposu kullanılamadığında aynı koşullar altında atar.
  
## <a name="signalr-and-session-state"></a>SignalR ve oturum durumu

SignalR uygulamaları bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalR uygulamaları bağlantı başına `Context.Items` durumu hub'da depolayabilir. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Rick [Anderson,](https://twitter.com/RickAndMSFT) [Steve Smith](https://ardalis.com/), Diana [LaRose](https://github.com/DianaLaRose)ve Luke [Latham](https://github.com/guardrex) tarafından

HTTP vatansız bir protokoldür. Ek adımlar atmadan, HTTP istekleri kullanıcı değerlerini veya uygulama durumunu korumayan bağımsız iletilerdir. Bu makalede, istekler arasında kullanıcı verilerini ve uygulama durumunu korumak için çeşitli yaklaşımlar açıklanmaktadır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Durum yönetimi

Durum çeşitli yaklaşımlar kullanılarak depolanabilir. Her yaklaşım daha sonra bu konuda açıklanmıştır.

| Depolama yaklaşımı | Depolama mekanizması |
| ---------------- | ----------------- |
| [Çerezler](#cookies) | HTTP çerezleri (sunucu tarafındaki uygulama kodu kullanılarak depolanan verileri içerebilir) |
| [Oturum durumu](#session-state) | HTTP çerezleri ve sunucu tarafı uygulama kodu |
| [Geçici Veriler](#tempdata) | HTTP çerezleri veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [httpContext.Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |
| [Bağımlılık Enjeksiyonu](#dependency-injection) | Sunucu tarafı uygulama kodu |

## <a name="cookies"></a>Tanımlama bilgileri

Çerezler verileri istekler arasında depolar. Çerezler her istekle gönderildiğinden, boyutları minimumda tutulmalıdır. İdeal olarak, uygulama tarafından depolanan verilerle birlikte bir çerezde yalnızca bir tanımlayıcı depolanmalıdır. Çoğu tarayıcı çerez boyutunu 4096 baytile kısıtlar. Her etki alanı için yalnızca sınırlı sayıda tanımlama bilgisi kullanılabilir.

Tanımlama bilgileri kurcalamaya tabi olduğundan, uygulama tarafından doğrulanmalıdır. Çerezler kullanıcılar tarafından silinebilir ve istemcilerin süresi dolabilir. Ancak, tanımlama bilgileri genellikle istemcideki en kalıcı veri biçimidir.

Çerezler genellikle içeriğin bilinen bir kullanıcı için özelleştirildiği kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlanır ve çoğu durumda kimliği doğrulanmaz. Çerez, kullanıcının adını, hesap adını veya benzersiz kullanıcı kimliğini (GUID gibi) depolayabilir. Daha sonra, kullanıcının tercih ettikleri web sitesi arka plan rengi gibi kişiselleştirilmiş ayarlarına erişmek için çerezi kullanabilirsiniz.

Çerezleri ve gizlilik sorunlarıyla uğraşırken [Avrupa Birliği Genel Veri Koruma Yönetmeliklerine (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) dikkat edin. Daha fazla bilgi için [ASP.NET Core'daki Genel Veri Koruma Yönetmeliği (GDPR) desteğine](xref:security/gdpr)bakın.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, kullanıcı bir web uygulamasına göz atarken kullanıcı verilerinin depolanması için ASP.NET bir Çekirdek senaryosudur. Oturum durumu, bir istemciden gelen istekler arasında verileri sürdürmek için uygulama tarafından tutulan bir depokullanır. Oturum verileri bir önbellek tarafından desteklenen ve sitenin oturum verileri olmadan çalışmaya devam etmesi gereken geçici veriler&mdash;olarak kabul edilir. Kritik uygulama verileri kullanıcı veritabanında depolanmalı ve yalnızca performans optimizasyonu olarak oturumda önbelleğe alınmalıdır.

> [!NOTE]
> [SignalR Hub,](xref:signalr/hubs) HTTP bağlamından bağımsız olarak yürütebileceğinden, Oturum [SignalR](xref:signalr/index) uygulamalarında desteklenmez. Örneğin, bu, uzun bir yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda oluşabilir.

ASP.NET Core, istemciye her istekle birlikte uygulamaya gönderilen oturum kimliği içeren bir tanımlama bilgisi sağlayarak oturum durumunu korur. Uygulama, oturum verilerini almak için oturum kimliğini kullanır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum çerezi tarayıcıya özgü olduğundan, oturumlar tarayıcılar arasında paylaşılmaz.
* Oturum çerezleri tarayıcı oturumu sona erdiğinde silinir.
* Süresi dolmuş bir oturum için çerez alınırsa, aynı oturum çerezini kullanan yeni bir oturum oluşturulur.
* Boş oturumlar tutulmaz,&mdash;oturumda istekler arasında oturumu devam ettirebilmek için en az bir değer ayarlanmalıdır. Bir oturum korunmuyorsa, her yeni istek için yeni bir oturum kimliği oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre için bir oturum tutar. Uygulama oturum zaman anına ayarlar veya varsayılan değeri 20 dakika kullanır. Oturum durumu, belirli bir oturuma özgü ancak verilerin oturumlar arasında kalıcı depolama gerektirmediği kullanıcı verilerini depolamak için idealdir.
* [Oturum verileri, ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) uygulaması çağrıldığında veya oturumun süresi dolduğunda silinir.
* Uygulama kodunu istemci tarayıcısının kapatıldığını veya oturum çerezinin istemci üzerinde silindiğini veya süresi nin dolduğunu bildiren varsayılan bir mekanizma yoktur.
* Core MVC ve Razor sayfaları ASP.NET şablonları Genel Veri Koruma Yönetmeliği (GDPR) desteğini içerir. Oturum durum tanımlama bilgileri varsayılan olarak önemli olarak işaretlenmez, bu nedenle izleme site ziyaretçisi tarafından izin verilmedikçe oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Hassas verileri oturum durumunda depolamayın. Kullanıcı tarayıcıyı kapatıp oturum çerezini temizlemeyebilir. Bazı tarayıcılar tarayıcı pencerelerinde geçerli oturum tanımlama bilgilerini korur. Bir oturum, bir sonraki kullanıcının aynı oturum çereziyle uygulamaya göz atmaya devam edebileceği tek bir kullanıcıyla&mdash;sınırlı olmayabilir.

Bellek önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Bir sunucu çiftliği senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure Uygulama Hizmeti,](https://azure.microsoft.com/services/app-service/) yapışkan oturumları varsayılan olarak zorlamak için [Uygulama İsteği Yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve web uygulaması güncelleştirmelerini karmaşıklaştırabilir. Daha iyi bir yaklaşım yapışkan oturumları gerektirmeyen bir Redis veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum [çerezi IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)üzerinden şifrelenir. Veri Koruması, her makinede oturum tanımlama bilgilerini okumak için düzgün bir şekilde yapılandırılmalıdır. Daha fazla bilgi <xref:security/data-protection/introduction> için bkz ve [Anahtar depolama sağlayıcıları.](xref:security/data-protection/implementation/key-storage-providers)

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer alan [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi, oturum durumunu yönetmek için ara yazılım sağlar. Oturum ara sını etkinleştirmek için aşağıdakileri `Startup` içermelidir:

* [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) bellek önbelleklerinden herhangi biri. Uygulama, `IDistributedCache` oturum için destek deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) için `ConfigureServices`bir çağrı .
* 'de `Configure` [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) için bir çağrı

Aşağıdaki kod, bellek içi oturum sağlayıcısının varsayılan bellek içi uygulamasıyla `IDistributedCache`nasıl ayarlanır:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Ara yazılım sırası önemlidir. Önceki örnekte, sonra `InvalidOperationException` `UseMvc`çağrıldığızaman `UseSession` bir özel durum oluşur. Daha fazla bilgi için [Middleware Ordering'e](xref:fundamentals/middleware/index#order)bakın.

[HttpContext.Session,](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce `UseSession` erişilemez.

Uygulama yanıt akışına yazmaya başladıktan sonra yeni oturum çerezi içeren yeni bir oturum oluşturulamaz. Özel durum web sunucusu günlüğüne kaydedilir ve tarayıcıda görüntülenmez.

### <a name="load-session-state-asynchronously"></a>Yük oturumu durumu eşzamanlı olarak

ASP.NET Core'daki varsayılan oturum sağlayıcısı, alttaki [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) destek deposundan yalnızca [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)veya [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) yöntemlerinden önce açıkça çağrıldığı takdirde oturum kayıtlarını yükler. [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) Önce `LoadAsync` çağrılmazsa, temel oturum kaydı eşzamanlı olarak yüklenir ve bu da ölçekte bir performans cezasına neden olabilir.

Uygulamaların bu deseni uygulamasına sahip olması için [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) ve [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) `LoadAsync` uygulamalarını, yöntem daha `TryGetValue` `Set`önce `Remove`çağrılmıyorsa özel durum oluşturan sürümlerle sarın, veya . Sarılmış sürümleri hizmet konteynerine kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için [SessionOptions'ı](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)kullanın.

| Seçenek | Açıklama |
| ------ | ----------- |
| [Çerez](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Tanımlama bilgisini oluşturmak için kullanılan ayarları belirler. [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`) için [ad](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) varsayılanları. [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) () için`/` [yol](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) varsayılandır. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) varsayılan [sameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) `1`( ). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan `true`. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) varsayılan `false`. |
| [ıdletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Oturum, `IdleTimeout` içeriği terk edilmeden önce ne kadar süre yle boşta kalarak durabileceğini gösterir. Her oturum erişimi zaman anına sıfırlanır. Bu ayar yalnızca çerez için değil, oturumun içeriği için geçerlidir. Varsayılan değer 20 dakikadır. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Bir oturumu niçin depodan yüklemeye veya mağazaya geri işlemek için izin verilen maksimum süre. Bu ayar yalnızca eşzamanlı işlemler için geçerli olabilir. Bu zaman aralığı [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan)kullanılarak devre dışı alınabilir. Varsayılan değer 1 dakikadır. |

Session, tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir çerez kullanır. Varsayılan olarak, bu `.AspNetCore.Session`çerez adlanır ve `/`bir yol kullanır. Çerez varsayılan bir etki alanı belirtmedığından, sayfadaki istemci tarafındaki komut dosyası için kullanılamaz (çünkü [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan `true`olarak).

Çerez oturumu varsayılanlarını geçersiz `SessionOptions`kılmak için şunları kullanın:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Uygulama, sunucunun önbelleğindeki içeriği terk edilmeden önce bir oturumun ne kadar süreyle boşta bırakılabildiğini belirlemek için [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) özelliğini kullanır. Bu özellik çerez son kullanma tarihinden bağımsızdır. [Session Middleware'den](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) geçen her istek zaman anına sıfırlar.

Oturum durumu *kilitlenmez.* İki istek aynı anda bir oturumun içeriğini değiştirmeye çalışırsa, son istek ilkini geçersiz kılar. `Session`tutarlı bir *oturum*olarak uygulanır, bu da tüm içeriğin birlikte depolandığı anlamına gelir. İki istek farklı oturum değerlerini değiştirmek istediğinde, son istek ilki tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve alma

Oturum durumuna, [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session)ile Bir Jilet Sayfaları [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) sınıfından veya MVC [Denetleyici](/dotnet/api/microsoft.aspnetcore.mvc.controller) sınıfından erişilir. Bu özellik bir [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) uygulamasıdır.

Uygulama, `ISession` tamsayı ve dize değerlerini ayarlamak ve almak için çeşitli uzantı yöntemleri sağlar. Uzantı yöntemleri [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) ad alanında (uzantı yöntemlerine erişmek için bir `using Microsoft.AspNetCore.Http;` deyim ekleyin) [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) paketi proje tarafından başvurulan bulunmaktadır. Her iki paket de [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahildir.

`ISession`uzatma yöntemleri:

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Aşağıdaki örnek, Bir Razor Pages `IndexModel.SessionKeyName` sayfasında`_Name` anahtar (örnek uygulamada) için oturum değerini alır:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve dize nasıl ayarlanıp alınır:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek önbelleği kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek için seri hale getirilmelidir. String ve insalı seriizanlar [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)uzantısı yöntemleri ile sağlanır). Karmaşık türleri JSON gibi başka bir mekanizma kullanılarak kullanıcı tarafından seri hale getirilmelidir.

Serileştirilebilir nesneleri ayarlamak ve almak için aşağıdaki uzantı yöntemlerini ekleyin:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, uzantı yöntemleriyle serileştirilebilir bir nesnenin nasıl ayarlanıp alınıldığını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>Geçici Veriler

ASP.NET Core, Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller'ı <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>ortaya çıkarır. Bu özellik, verileri başka bir istekte okunana kadar depolar. [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri, isteğin sonunda silinmeden verileri incelemek için kullanılabilir. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) bekletme için sözlükteki tüm öğeleri işaretler. `TempData`birden fazla istek için veri gerektiğinde yeniden yönlendirme için özellikle yararlıdır. `TempData`tanımlama bilgileri `TempData` veya oturum durumu kullanılarak sağlayıcılar tarafından uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa `TempData["Message"]`görüntülenir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki biçimlendirmede, isteğin sonunda, `TempData["Message"]` kullanılmadan dolayı `Peek` **silinmez.** Sayfayı yenileme `TempData["Message"]`ekranı .

Aşağıdaki biçimlendirme önceki koda benzer, ancak `Keep` isteğin sonundaverileri korumak için kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*IndexPeek* ve *IndexKeep* sayfaları arasında gezinme `TempData["Message"]`siler.

Aşağıdaki kod `TempData["Message"]`görüntülenir, ancak isteğin sonunda, `TempData["Message"]` silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

Çerez tabanlı TempData sağlayıcısı, TempData'yı tanımlama bilgilerinde depolamak için varsayılan olarak kullanılır.

Çerez verileri [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kullanılarak şifrelenir, [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder)ile kodlanır , sonra öden. Çerez tıklım tıklım olduğundan, Core 1.x'ASP.NET bulunan tek çerez boyutu sınırı geçerli değildir. Şifrelenmiş verileri sıkıştırmak [SUÇ](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [İhLAL](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açabileceğinden çerez verileri sıkıştırılmış değildir. Çerez tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için [CookieTempDataProvider'a](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider)bakın.

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

TempData sağlayıcısı seçmek, şu gibi çeşitli hususlar içerir:

1. Uygulama zaten oturum durumunu kullanıyor mu? Bu durumda, oturum durumu TempData sağlayıcısını kullanmanın uygulamaya ek bir maliyeti yoktur (verilerin boyutu dışında).
2. Uygulama TempData'yı yalnızca nispeten küçük miktarda veri (500 baytkadar) için kullanıyor mu? Bu nedenle, çerez TempData sağlayıcısı TempData taşıyan her istek için küçük bir maliyet ekler. Değilse, oturum durumu TempData sağlayıcısı, TempData tüketilene kadar her istekte büyük miktarda veriyi yuvarlatmayı önlemek için yararlı olabilir.
3. Uygulama birden çok sunucudaki bir sunucu çiftliğinde mi çalışır? Bu nedenle, Veri Koruması dışında çerez TempData sağlayıcısını kullanmak için <xref:security/data-protection/introduction> ek yapılandırma gerekmez (bkz. ve [Anahtar depolama sağlayıcıları).](xref:security/data-protection/implementation/key-storage-providers)

> [!NOTE]
> Çoğu web istemcisi (web tarayıcıları gibi) her çerezin maksimum boyutu, toplam tanımlama bilgisi sayısı veya her ikisi yle ilgili sınırlar uygular. Çerez TempData sağlayıcısını kullanırken, uygulamanın bu sınırları aşmayacağını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. Şifreleme ve yığınlama nedeniyle çerez boyutundaki artışları hesaba katın.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

Çerez tabanlı TempData sağlayıcısı varsayılan olarak etkinleştirilir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) uzantı yöntemini kullanın:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Ara yazılım sırası önemlidir. Önceki örnekte, sonra `InvalidOperationException` `UseMvc`çağrıldığızaman `UseSession` bir özel durum oluşur. Daha fazla bilgi için [Middleware Ordering'e](xref:fundamentals/middleware/index#order)bakın.

> [!IMPORTANT]
> .NET Framework'u hedefliyorsanız ve oturum tabanlı TempData sağlayıcısını kullanıyorsanız, [projeye Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketini ekleyin.

## <a name="query-strings"></a>Sorgu dizeleri

Sınırlı miktarda veri, yeni isteğin sorgu dizesine eklenerek bir istekten diğerine aktarılabilir. Bu, e-posta veya sosyal ağlar aracılığıyla katıştırılmış durumla bağlantının paylaşılmasına olanak tanıyan kalıcı bir şekilde durumu yakalamak için yararlıdır. URL sorgu dizeleri herkese açık olduğundan, hassas veriler için sorgu dizelerini asla kullanmayın.

Sorgu dizelerinde veri de dahil olmak üzere istenmeyen paylaşıma ek olarak, siteler [arası istek sahteciliği (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırıları için fırsatlar oluşturabilir ve bu da kullanıcıları kimlik doğrulaması yapılırken kötü amaçlı siteleri ziyaret etmeye kandırabilir. Saldırganlar daha sonra uygulamadan kullanıcı verilerini çalabilir veya kullanıcı adına kötü amaçlı eylemlerde bulunabilir. Korunmuş herhangi bir uygulama veya oturum durumu CSRF saldırılarına karşı korumalıdır. Daha fazla bilgi için [bkz.](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve bir sonraki isteğe nakledilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verileri kurcalayabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden geçersiz kılmalıdır.

## <a name="httpcontextitems"></a>httpContext.Items

[HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) koleksiyonu, tek bir isteği işlerken verileri depolamak için kullanılır. Bir istek işlendikten sonra koleksiyonun içeriği atılır. Koleksiyon `Items` genellikle bileşenlerin veya ara yazılımların istek sırasında farklı noktalarda çalıştıklarında iletişim kurmasına izin vermek için kullanılır ve parametreleri aktarmanın doğrudan bir yolu yoktur.

Aşağıdaki örnekte, [ara](xref:fundamentals/middleware/index) `isVerified` yazılım `Items` koleksiyona ekler.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Daha sonra boru hattı, başka bir `isVerified`ara değeri erişebilirsiniz:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Yalnızca tek bir uygulama tarafından kullanılan ara `string` yazılımlar için tuşlar kabul edilebilir. Uygulama örnekleri arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne tuşlarını kullanmalıdır. Aşağıdaki örnek, ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, ara yazılım `HttpContext.Items` sınıfıtarafından açığa çıkarılan anahtarı kullanarak depolanan değere erişebilir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım, koddaki anahtar dizelerin kullanımını ortadan kaldırma avantajına da sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamanın ve almanın etkili bir yoludur. Uygulama önbelleğe alınmış öğelerin kullanım ömrünü denetleyebilir.

Önbelleğe alınan veriler belirli bir istek, kullanıcı veya oturumla ilişkili değildir. **Diğer kullanıcıların istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe almamaya dikkat edin.**

Daha fazla bilgi için bkz. <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Bağımlılık Ekleme

Verileri tüm kullanıcıların kullanımına açmak için Bağımlılık Enjeksiyonu'nunu kullanın: [Dependency Injection](xref:fundamentals/dependency-injection)

1. Verileri içeren bir hizmet tanımlayın. Örneğin, adlı `MyAppData` bir sınıf tanımlanır:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Hizmet sınıfını `Startup.ConfigureServices`ekle:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Veri hizmeti sınıfını tüketin:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Sık karşılaşılan hatalar

* "Microsoft.AspNetCore.Session.DistributedSessionStore'u etkinleştirmeye çalışırken 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' türü için hizmet çözümlenemiyor."

  Bu genellikle en az bir `IDistributedCache` uygulama yapılandırmak için başarısız kaynaklanır. Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

* Oturum ara yazılımının bir oturumu sürdürememesi durumunda (örneğin, destek deposu yoksa), ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder. Bu öngörülemeyen davranışlara yol açar.

  Örneğin, bir kullanıcı oturumda bir alışveriş sepeti depolar. Kullanıcı sepete bir öğe ekler, ancak işleme başarısız olur. Uygulama nın hatadan haberi olmadığı için kullanıcıya öğenin sepetine eklendiği bildirildiği ve bu doğru değildir.

  Hataları denetlemek için önerilen yaklaşım, `await feature.Session.CommitAsync();` uygulama oturuma yazma bittiğinde uygulama kodundan aramaktır. `CommitAsync`destek deposu kullanılamıyorsa bir özel durum oluşturur. Başarısız `CommitAsync` olursa, uygulama özel durumu işleyebilir. `LoadAsync`veri deposunun kullanılamadığı koşullar altında atar.
  
## <a name="opno-locsignalr-and-session-state"></a>SignalRve oturum durumu

SignalRuygulamalar bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalRuygulamalar bağlantı durumu başına `Context.Items` hub'da depolanabilir. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
::: moniker-end
